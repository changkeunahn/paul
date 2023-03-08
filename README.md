# paul
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
        ![image](https://user-images.githubusercontent.com/121933672/223622035-238f0a8d-afa3-4ab7-b64d-406339bdcf13.png)
 
  - Step. 4: StorageClass 생성 
        ![image](https://user-images.githubusercontent.com/121933672/223622305-4976882b-0b5e-4798-8e21-79a1113f6f1d.png)

  - Step. 5: PVC 생성   
    ![image](https://user-images.githubusercontent.com/121933672/223622259-9912f848-a9a1-48bc-a11f-d9df95989d72.png)
    정상적으로 mount되었는지 확인   
    ![image](https://user-images.githubusercontent.com/121933672/223622343-5a5300d9-327a-46d1-aa39-3bb594396a16.png)
