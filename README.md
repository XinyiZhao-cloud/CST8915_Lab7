# CST8915 Lab 7: Introduction to Kubernetes Basics
**Student Name**: Xinyi Zhao    
**Student ID**: 040953633    
**Course**: CST8915 Full-stack Cloud-native Development    
**Semester**: Winter 2026    

---

## Demo Video

🎥 https://youtu.be/Ff2o1xy092k

---

## Technical Explanations

### Whether RabbitMQ is a stateless or stateful application
RabbitMQ is a stateful application. It is not just a simple service that responds to requests and forgets everything right away. It manages queues, exchanges, bindings, and message data, so it needs to keep track of state over time. In many cases, RabbitMQ is expected to preserve messages and queue information even if the service restarts. Because of that, it should be considered stateful rather than stateless.

### The implications of running RabbitMQ without persistent storage
Running RabbitMQ in Kubernetes without persistent storage is risky. In this case, its data is tied to the pod or container filesystem. If the pod is deleted, restarted, or rescheduled, that data may disappear as well. This means queue contents, message data, or other broker state could be lost. As a result, the messaging system becomes less reliable and is not a good design for workloads that require durability.

### What happens when the RabbitMQ pod is deleted or restarted
When the RabbitMQ pod is deleted, Kubernetes automatically creates a new one because it is managed by a Deployment. In this lab, after the new pod was recreated, the RabbitMQ management UI was no longer accessible from the browser session I had been using before. This shows that pod recreation can interrupt service access. More importantly, because RabbitMQ is stateful and no persistent storage is configured, there is also a risk that queued messages or broker state could be lost after a restart or pod recreation.

### Potential solutions to this problem (research-based)
A better design would be to run RabbitMQ with persistent storage and use a Kubernetes pattern that is more suitable for stateful applications. One improvement would be to attach a PersistentVolume (PV) and PersistentVolumeClaim (PVC) so the data can survive even if the pod is replaced. Another improvement would be to use a StatefulSet instead of a normal Deployment, because a StatefulSet is designed for applications that need a stable identity and stable storage. In addition, RabbitMQ durability features such as durable queues and persistent messages should also be configured properly. For stronger reliability, features like quorum queues could also be considered.

### Does using Azure Service Bus solve the issues identified with RabbitMQ Configuration in this Lab?
For the problem shown in this lab, I would say mostly yes. Azure Service Bus is a fully managed messaging service, so it already handles durability, reliability, and the underlying infrastructure. That means I would not need to worry about pod restarts, container storage, or managing RabbitMQ inside Kubernetes. In that sense, it does solve the main issue shown in this lab. However, Azure Service Bus is not exactly the same as RabbitMQ, so it is not always a direct replacement. Moving from RabbitMQ to Azure Service Bus may still require some changes to the application design and implementation.
