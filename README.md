# Multi Cloud Gateway Standalone deployment 
Repo to deploy ODF Multi-Cloud Gateway standalone using an argoCD application

If you don't have a running instance of argocd on your OCP cluster you can run
the setup.sh script available in the repo, it will setup a basic
openshift-gitops instance with the mcg application.

1. Fork & Clone the repo 'https://github.com/likid0/mcgstandalone', using the
   gh cli in this example:

```
$ gh repo fork git@github.com:likid0/mcgstandalone.git --clone
```

2. Make modifications as needed to the argocd deployment.
```
$ vi argocd/values.yaml
```
3. If you have enabled the deployment of LVMO in the argocd/values.yaml file, Make modifications to the lvmo/values.yaml file

```
$ vi lvmo/values.yaml
```
3. Make modifications to the MCG configuration as needed.
```
$ vi mcg/values.yaml
```
4. Push the modifications to your forked github repo.
5. run the setup.sh script.
```
$ bash setup.sh
```
6. You can check that the phase state is Ready to verify MCG is up and running:
```
$ kubectl get noobaa noobaa -o jsonpath='{.status.phase}{"\n"}'
```
7. MCG is ready to be used a default user has been created for testing,
   configure your S3 client:

```
$ NOOBAA_ACCESS_KEY=$(kubectl get secret noobaa-account-demo -n openshift-storage -o json | jq -r '.data.AWS_ACCESS_KEY_ID|@base64d')
$ NOOBAA_SECRET_KEY=$(kubectl get secret noobaa-account-demo -n openshift-storage -o json | jq -r '.data.AWS_SECRET_ACCESS_KEY|@base64d')
$ S3EXTENDPOINT=$(kubectl get route s3  -o jsonpath='{.spec.host}{"\n"}' -n openshift-storage)
$ alias s3='AWS_ACCESS_KEY_ID=$NOOBAA_ACCESS_KEY AWS_SECRET_ACCESS_KEY=$NOOBAA_SECRET_KEY aws --endpoint https://$S3EXTENDPOINT --no-verify-ssl s3'
$ s3 ls
```
