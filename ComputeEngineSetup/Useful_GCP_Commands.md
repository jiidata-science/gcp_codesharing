
### CONNECT TO VM AND LAUNCH RSTUDIO IN LOCAL BROWSER
**Template :**
```
gcloud compute --project "<project-id>" ssh --zone "<gcp-processing-zone>" "<compute-engine-name>" -- -L localhost:<xxxx>:localhost:<xxxx>
```
**Example :**
```
gcloud compute --project "big-data-project" ssh --zone "europe-west4-a" "my-vm" -- -L localhost:8787:localhost:8787
```

### SSH INTO VM AND RUN COMMANDS
**Template :**
```
gcloud compute ssh --project=<project-id> --zone=<gcp-processing-zone> <compute-engine-name> -- "<bash commands to run>"
```
