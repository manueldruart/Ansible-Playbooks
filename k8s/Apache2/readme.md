<h1>Deploy Apache2 Services to your K8S cluster</h1>

<h2>deploy.yaml</h2>

<p>Make sure to change the Namespaces to your Namespace. In this example 2 replicas will be created, modify this as you need. the containers image can be changed as you need, you can check on dockerhub to choose the appropriate image tag</p>

<h2>pod.yaml</h2>

<p>Make sure to change the Namespaces to your Namespace. the containers image can be changed as you need, you can check on dockerhub to choose the appropriate image tag</p>

<h2>apache2.yaml</h2>

<p>This playbook will first copy the pod.yaml and deployment.yaml to a tmp folder. It will then check if the pods are running. </p>