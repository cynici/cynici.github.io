# Nifty gcloud Zsh/Bash functions

From a colleague. Replace `my-...` values with convenient default.

```
function gcp-project-names() {
    local filter=$1
    gcloud projects list --filter="${filter}" --format="table[no-heading](name)"
}

function gcp-instances() {
    proj=${1:=my-gcp-project}
    gcloud compute instances list --format="table(name)" --project "$proj"
}
function gcp-fwdrules() {
    local filter=$1
    local proj=${2:=my-gcp-project}
    gcloud compute forwarding-rules list --filter="${filter}" --format="yaml" --project "$proj"
}

function gcp-igs() {
    local filter=$1
    local proj=${2:=my-gcp-project}
    gcloud compute instance-groups list --filter="${filter}" --format="yaml" --project "$proj"
}

function tail-serial-port() {
    local instance=$1
    local proj=${2:=my-gcp-project}
    gcloud compute instances tail-serial-port-output "$instance" --zone europe-west1-b --project "$proj"
}

function gcp-subnets() {
    list=$(gcp-project-names)
    while IFS= read -r line; do
        echo GCP_PROJECT: "${line}"
        gcloud compute networks subnets list --project "${line}" --regions europe-west1
    done <<< "$list"
}

function gke-autoscaler-events() {
    gcloud logging read "logName:cluster-autoscaler-visibility" --project my-gke-project\
     --format="table(timestamp,jsonPayload.decision.scaleDown,jsonPayload.decision.scaleUp)"
}

function gcp-instance-preempted() {
    local proj=${1:=my-gke-project}
    gcloud logging read "resource.type=gce_instance AND
    protoPayload.methodName=compute.instances.preempted" --project $proj\
     --format="yaml(protoPayload,timestamp)"
}

function gcp-ssh() {
    local proj=${1:=my-gcp-project}
    if ! _find_fingerprint; then
        gcp-ssh-withadd $proj
        exit $?
    fi
    gcloud compute --project $proj ssh --zone europe-west1-b --ssh-flag="-A" bastion
}

function gcp-ssh-withadd() {
    echo "adding ~/.ssh/google_compute_engine to agent"
    ssh-add ~/.ssh/google_compute_engine
    local proj=${1:=my-gcp-project}
    gcloud compute --project $proj ssh --zone europe-west1-b --ssh-flag="-A" bastion
}

function _find_fingerprint() {
    _fingerprints="$(ssh-add -l)"
    _gce_fingerprint="$(ssh-keygen -lf ~/.ssh/google_compute_engine)"
    if [[ "${_fingerprints}" = *"${_gce_fingerprint}"* ]]; then
        echo "figerprint confirmed"
        return 0
    else
        return 1
    fi
}

function gcp-firewallrules() {
    local proj=${1:=my-gcp-project}
     gcloud compute firewall-rules list --format="yaml(
        name,
        network,
        direction,
        priority,
        sourceRanges.list():label=SRC_RANGES,
        destinationRanges.list():label=DEST_RANGES,
        allowed[].map().firewall_rule().list():label=ALLOW,
        denied[].map().firewall_rule().list():label=DENY,
        sourceTags.list():label=SRC_TAGS,
        sourceServiceAccounts.list():label=SRC_SVC_ACCT,
        targetTags.list():label=TARGET_TAGS,
        targetServiceAccounts.list():label=TARGET_SVC_ACCT,
        disabled
    )" --project $proj
}

function gke-get-credentials() {
    local cluster=$1
    local environment=$2
    local region="europe-west1-b"
    gcloud container clusters get-credentials ${cluster} --region ${region} --project my-gke-project
}
```
