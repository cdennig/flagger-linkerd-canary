**Sample repository for: https://partlycloudy.blog/?p=2243**

If you want to deploy that sample application to K8s, do it in the following order:

- install linkerd
- add a namespace "quotes"
- annotate the namespace for auto-injection of the linkerd sidecar
- add nginx ingress controller (note down the *public IP* of the ingress controller)
- kubectl apply -f base-backend-infra.yaml -n quotes
- kubectl apply -f base-backend-app.yaml -n quotes
- adjust *settings_template.js* using the *public IP* from the ingress controller
- Add a secret to configure the endpoint for the Single Page Application (secret will be mounted, see *base-frontend-app.yaml*):
  - kubectl create secret generic uisettings --from-file=settings.js=./settings_template.js -n quotes
- kubectl apply -f base-frontend-app.yaml -n quotes
- kubectl apply -f ingress.yaml -n quotes

When everything is successfully deployed, you should see a SPA app creating Star Wars quotes.

Flagger integration:

- install Flagger
- kubectl apply -f canary.yaml -n quotes (wait until "canary" is successfully initialized)

Trigger canary deployment:

- kubectl apply -f st-backend-app.yaml

Provoke rollback of a canary deployment (after 1 minute, errors are thrown by the app):

- kubectl apply -f error-backend-app.yaml
