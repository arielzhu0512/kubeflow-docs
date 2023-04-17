=======================================
Tensorflow Distributed Training (TFJob)
=======================================

Introduction
============

``TFJob`` is a training operator in Kubeflow that is specifically designed to run distributed TensorFlow training jobs on Kubernetes clusters. It provides a simple and consistent way to define, manage, and scale TensorFlow training jobs in a distributed manner, allowing users to easily leverage the power of Kubernetes to accelerate their machine learning workloads.

With ``TFJob``, users can define a TensorFlow training job as a YAML configuration file, specifying the details of the job such as the number of worker and parameter servers, the location of the training data, the type of cluster to use, and more. ``TFJob`` then creates and manages the Kubernetes resources required to run the job, including pods, services, and volumes.

``TFJob`` also supports advanced features such as distributed training with data parallelism, model parallelism, and synchronous or asynchronous updates, as well as monitoring and visualization of training metrics using TensorBoard. This makes it a powerful tool for running large-scale TensorFlow training jobs on Kubernetes clusters, whether on-premises or in the cloud.


Get started
===========

In this tutorial, we'll create a training job by defining a ``TFJob`` config file to train a model in the terminal. Before that, we’ll need a working Kubeflow deployment with TFJob Operator up and running. 


Verify TFJob running
--------------------

Check that the Tensorflow custom resource is installed:

.. code-block:: shell

    $ kubectl get crd
    NAME                                             CREATED AT
    ...
    tfjobs.kubeflow.org                         2023-01-31T06:02:59Z
    ...

Check that the Training operator is running via:

.. code-block:: shell

    $ kubectl get pods -n kubeflow
    NAME                                READY   STATUS    RESTARTS   AGE
    ...
    training-operator-0                 2/2     Running   4 (6d1h ago)    6d2h
    ...


Create a TF training job
------------------------

You can create a training job by defining a ``TFJob`` config file. See the manifests for the mnist example. You may change the config file based on your requirements.

You can deploy the ``TFJob`` resource with **CPU** and **GPU**, but we just provide YAML file with **CPU** to deploy ``TFJob`` due to certain reasons. Thus, you can follow the step to deploy the ``TFJob`` resource with **CPU**. If you want to deploy ``TFJob`` resource with **GPU**, please refer to `TFJob deployment using GPUs <https://www.kubeflow.org/docs/components/training/tftraining/#using-gpus>`_.

You can deploy the TFJob resource with **CPU** to start training:

.. code-block:: shell

  USER_NAMESPACE=user
  kubectl config set-context --current --namespace=$USER_NAMESPACE

  # Deploy the TFJob resource with CPU
  cat <<EOF | kubectl create -n $USER_NAMESPACE -f -
  apiVersion: "kubeflow.org/v1"
  kind: TFJob
  metadata:
    name: tfjob-simple
  spec:
    tfReplicaSpecs:
      Worker:
        replicas: 2
        restartPolicy: OnFailure
        template:
          metadata:
            annotations:
              sidecar.istio.io/inject: "false"
        template:
          spec:
            containers:
              - name: tensorflow
                image: projects.registry.vmware.com/models/kubeflow-docs/model-training-tf-mnist-with-summaries:1.0
                command:
                  - "python"
                  - "/var/tf_mnist/mnist_with_summaries.py"
  EOF

You should now be able to see the created pods matching the specified number of replicas.

.. code-block:: shell

    $ kubectl get pods -l job-name=tfjob-simple -n $USER_NAMESPACE


Monitoring a TFJob
------------------

Check the events for your job to see if the pods were created.

.. code-block:: shell

    $ kubectl describe tfjobs tfjob-simple -n $USER_NAMESPACE
    ...
    Events:
    Type    Reason                   Age                From              Message
    ----    ------                   ----               ----              -------
    Normal  SuccessfulCreatePod      78s                tfjob-controller  Created pod: tfjob-simple-worker-0
    Normal  SuccessfulCreatePod      77s                tfjob-controller  Created pod: tfjob-simple-worker-1
    Normal  SuccessfulCreateService  77s                tfjob-controller  Created service: tfjob-simple-worker-0
    Normal  SuccessfulCreateService  77s                tfjob-controller  Created service: tfjob-simple-worker-1

Check the logs to see the training result when the training process completed.

.. code-block:: shell

    $ kubectl logs -f tfjob-simple-worker-0 -n $USER_NAMESPACE
    $ kubectl logs -f tfjob-simple-worker-1 -n $USER_NAMESPACE

.. seealso::

   `Using TFJob to train a model with TensorFlow <https://www.kubeflow.org/docs/components/training/tftraining/#running-the-mnist-example>`_
