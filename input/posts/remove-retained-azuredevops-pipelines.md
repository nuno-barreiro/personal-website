---
Title: Remove retained Azure DevOps pipelines
Published: 2021-12-22 15:00:00 +0000
Tags: 
  - Azure DevOps
---

One situation I've come across a while ago was not being able to remove some deprecated pipelines due to the following error:

![One or more builds associated with the requested pipeline(s) are retained by a release. The pipeline(s) and builds will not be deleted.](/input/images/devops-pipeline-retention-lease-error-onremoval.png)

Going through the [builds REST documentation](https://docs.microsoft.com/en-us/rest/api/azure/devops/build/builds/get?view=azure-devops-rest-6.0), I was able to check that Builds do have a property "retainedByRelease". Not only that, but digging further and looking at the [leases REST documentation](https://docs.microsoft.com/en-us/rest/api/azure/devops/build/leases?view=azure-devops-rest-6.1) I also noticed a boolean property named "protectPipeline" that, according to its very relevant description states that:

> If set, this lease will also prevent the pipeline from being deleted while the lease is still valid.

Now I was finally getting somewhere, and I've attempted to patch the lease and set that property to false. Surprised, or maybe not, when you set that property to false for all the leases associated with the build, then the build own property "retainedByRelease" also turns out to false, allowing you to finally delete it permanently!

To automate things, since I had multiple builds with the same state, I've created the below python script, that you are invited to read and use. 
At the time I was using [Fiddler](https://www.telerik.com/fiddler) to check the requests, but you can remove the "fiddler_proxies" related stuff from there if you don't need that.

Please follow the [instructions](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) on how to create a Personal Access Token and encode it using a base64 encoding tool like [this](https://www.base64encode.org) one.

Make sure to fill in the "project_url", the "encoded_pat" and the "build_definition_ids" variables, before running the script. You can get the build IDs from the URL when you enter a build definition using the DevOps portal

The project url can also be get from the address bar, and for this script it needs to be in the format: "https://dev.azure.com/{org name}/{project name}".

```python
import pprint 
import requests 

project_url = ""
encoded_pat = ""
build_definition_ids = [] 

base_api_url = f"{project_url}/_apis/build" 
request_headers = { "Authorization": f"Basic {encoded_pat}" } 
fiddler_proxies = { "http": "http://localhost:8888", "https": "http://localhost:8888" } 

def getBuildByDefinition(definition_id): 
    params = { "definitions": definition_id, "statusFilter": "completed", "api-version": "6.1-preview.7" } 
    response = requests.get(f"{base_api_url}/builds", headers=request_headers, params=params, proxies=fiddler_proxies, verify=False) 
    response.raise_for_status() 
    return response.json()["value"] 

def getBuildLeases(build_id): 
    params = { "api-version": "6.1-preview.1" } 
    response = requests.get(f"{base_api_url}/builds/{build_id}/leases", headers=request_headers, params=params, proxies=fiddler_proxies, verify=False) 
    response.raise_for_status() 
    return response.json()["value"] 

def deleteBuildLease(lease_id): 
    params = { "api-version": "6.1-preview.1" } 
    response = requests.delete(f"{base_api_url}/retention/leases?ids={lease_id}", headers=request_headers, params=params, proxies=fiddler_proxies, verify=False) 
    response.raise_for_status() 
    return response.status_code == 204 

for definition_id in build_definition_ids: 
    builds = getBuildByDefinition(definition_id) 
    for build in builds: 
        build_id = build["id"] 
        if build["retainedByRelease"] == True: 
            leases = getBuildLeases(build_id) 
            for lease in leases: 
                lease_id = lease["leaseId"] 
                if lease["protectPipeline"] == True: 
                    delete_result = deleteBuildLease(lease_id) 
                    pprint.pp(f"Lease {lease_id} of build {build_id} delete status => {delete_result}") 
        else: 
            pprint.pp(f"Build {build_id} is not retained by release") 
```

