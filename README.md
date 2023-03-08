# paul
[분석/설계]  
1. 주제별 Eventstorming 모델  

2. 시나리오 커버리지  


[구현/배포/운영]  
Saga (Pub-Sub)  
CQRS  
Compensation & Correlation  
Request-Response (Not implemented)  
Circuit Breaker (Not implemented)  
Gateway / Ingress  
Deploy / Pipeline  
Autoscale (HPA)  
Zero-downtime deploy (Readiness probe)  

Persistence Volume/ConfigMap/Secret  
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
 
 
  - Step. 4: StorageClass 생성 
 
  - Step. 5: PVC 생성
 
정상적으로 mount되었는지 확인
 


Self-healing (liveness probe)   
Apply Service Mesh    
Loggregation / Monitoring    
