# akka-java-cluster-istio

An Akka Java cluster Istio demo application. It requires Istio 1.2 (not yet released, the latest nightly should do).

## Building

A docker image is deployed to Docker hub which contains this application built, and the deployment descriptor references that. If you wish to build your own image, run:

```
mvn package docker:build
```

To first build the image, and then tag/push it to your repo using:

```
docker tag akka-cluster-demo:1.0.3 <your-docker-repo>/akka-cluster-demo:1.0.3
docker push <your-docker-repo>/akka-cluster-demo:1.0.3
```

Then update the `image` field in `kubernetes/akka-cluster-deployment.yml` to point to your image.

## Deploying

This configures an Istio gateway that routes all HTTP requests to the default Istio ingress to the Akka cluster app. That might not be what you want, if not, configure `kubernetes/akka-cluster-istio-service.yaml` accordingly.

To deploy this to the default namespace (change the `-n` argument for a different namespace):

```
kubectl apply -f kubernetes/ -n default
```

Now, assuming your Istio ingress has an externally accessible IP address, visit it (the IP address can be found be running `kubectl get service -n istio-system istio-ingressgateway`). You should see the Akka cluster sharding visualisation. You can kill a node by clicking on it, you will then see the shards get rebalanced to other nodes, and in a short time, the node will be restarted by Kubernetes, and shards will gradually be allocated back to it.