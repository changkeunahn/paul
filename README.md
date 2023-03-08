1. Saga (Pub-Sub)
Order 서비스에서 OrderPlaced 이벤트를 발행하면, Payment 서비스에서 OrderPlaced 이벤트를 수신하여 StartPayment 작업을 실행한다. 
StartPayment 작업은 Paid 이벤트를 발행하고, Café 서비스에서 Paid 이벤트를 수신하여 AddCafeOrder 작업을 실행한다.

Order 서비스를 호출하여 주문 요청시 OrderPlaced, Paid 토픽이 발행되며, 주문 이력이 cafe repository에 저장되는 것을 확인할 수 있다.

[주문 요청]
![image](https://user-images.githubusercontent.com/74826215/223603363-8de0a839-df63-4150-82aa-f01f20d2427e.png)


[이벤트 발행 확인]
![image](https://user-images.githubusercontent.com/74826215/223603384-0ff9116a-beb7-44ed-9d60-330a85609c6b.png)


[주문내역(café repository) 확인]
![image](https://user-images.githubusercontent.com/74826215/223603397-5be2659c-40b3-408d-b0f8-c73a016f16ae.png)


2. CQRS
주문 및 배달 현황을 확인할 수 있는 마이페이지 서비스를 위해 Read Model 생성
[MyPage]
 ![image](https://user-images.githubusercontent.com/74826215/223603407-2bf0968e-ce03-457e-84dc-feb7a10c86a5.png)
![image](https://user-images.githubusercontent.com/74826215/223603421-6a3617ca-3e65-48bb-83b7-c2241f33297d.png)
![image](https://user-images.githubusercontent.com/74826215/223603443-4a19306d-d236-490a-af5f-4cf141d2b083.png)

 

 
주문 요청 후 CQRS에 정의된 내용에 따라 주문/배달 상태가 변경되었음

3. 
CQRS
Compensation & Correlation
Request-Response (Not implemented)
Circuit Breaker (Not implemented)
Gateway / Ingress
Deploy / Pipeline
Autoscale (HPA)
Zero-downtime deploy (Readiness probe)
Persistence Volume/ConfigMap/Secret

## Persistence Volume/ConfigMap/Secret  
  EFS (Elastic File System) 사용을 위한 설정
  - Step. 1: EFS 생성 
    ![image](https://user-images.githubusercontent.com/121933672/223619655-38b0a7c7-d6cc-4d75-816d-9cdcd55ffc0f.png)
    ![image](https://user-images.githubusercontent.com/121933672/223619692-0fff160c-df69-4d84-bf55-35ad34b6241a.png)

  - Step. 2: EFS계정 생성 및 Role 바인딩 
     ServerAccount 생성   
     서비스 계정(efs-provisioner)에 권한(rbac) 설정   

  - Step. 3: EFS Provisioner 설치   
     efs-provisioner.yaml 편집하여 EKS에 EFS 프로비저너 설치  
        value: # fs-0955d6fce0c755475 => 파일 시스템 ID  
        value: # eu-central-1 => EKS 리전  
        server:# fs-0955d6fce0c755475.efs.eu-central-1.amazonaws.com => 파일 시스템 ID  
        ![image](https://user-images.githubusercontent.com/121933672/223622035-238f0a8d-afa3-4ab7-b64d-406339bdcf13.png)
 
  - Step. 4: StorageClass 생성 
        ![image](https://user-images.githubusercontent.com/121933672/223622305-4976882b-0b5e-4798-8e21-79a1113f6f1d.png)

  - Step. 5: PVC 생성   
    ![image](https://user-images.githubusercontent.com/121933672/223622259-9912f848-a9a1-48bc-a11f-d9df95989d72.png)
    정상적으로 mount되었는지 확인   
    ![image](https://user-images.githubusercontent.com/121933672/223622343-5a5300d9-327a-46d1-aa39-3bb594396a16.png)
    
    
## Self-healing (liveness probe)
Order 컨테이너에 장애가 생겼을 때, 컨테이너 플랫폼이 자동으로 장애를 감지하여 복구하도록 설정합니다.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order
  labels:
    app: order
spec:
  replicas: 1
  selector:
    matchLabels:
      app: order
  template:
    metadata:
      labels:
        app: order
    spec:
      containers:
        - name: order
          image: sjjo0319/order:230307
          ports:
            - containerPort: 8080
          resources:
            requests:
              cpu: "200m"
          readinessProbe:
            httpGet:
              path: '/actuator/health'
              port: 8080
            initialDelaySeconds: 20
            timeoutSeconds: 2
            periodSeconds: 5
            failureThreshold: 10 
          livenessProbe:
            httpGet:
              path: '/actuator/health'
              port: 8080
            initialDelaySeconds: 30
            timeoutSeconds: 2
            successThreshold: 1
            periodSeconds: 1
            failureThreshold: 5
          volumeMounts:
          - mountPath: "/mnt/data"
            name: volume
      volumes:
        - name: volume
          persistentVolumeClaim:
            claimName: aws-efs
```
- Liveness Probe를 확인합니다
![image](https://user-images.githubusercontent.com/74826215/223605978-3477d637-33cf-48fd-9335-fce7eab138c0.png)





## Apply Service Mesh

- istio를 설치하고 Kiali와 Jaeger를 기동합니다.
![image](https://user-images.githubusercontent.com/74826215/223606436-1c516913-2edb-434f-a589-0743df206b5d.png)
![image](https://user-images.githubusercontent.com/74826215/223606536-fb1bc43d-0c61-4185-be05-039ca0ec825a.png)


- istio를 설치하고 각 Pod에 SideCar를 Inject 합니다.
사용자 트래픽의 흐름이나 설정된 istio 구성요소들의 동작 상황을 실시간 감지하여 그래픽을 통해 모니터링 합니다.
![image](https://user-images.githubusercontent.com/74826215/223606246-b0439666-50d2-467d-b4f7-ddd83d6c8f36.png)


Loggregation / Monitoring
