###helm环境

helm repo add azure http://mirror.azure.cn/kubernetes/charts/
helm repo update

helm pull elastic/elasticsearch
tar -zvf elasticsearch


- helm repo list 
  NAME            URL
  elastic         https://helm.elastic.co

修改配置
根据实际情况修改配置vi elasticsearch/values.yml
        #replicas: 1 ###修改单点
        #minimumMasterNodes: 1 ###修改单点
+ 安装elastirsearch
  helm install   elasticsearch   elasticsearch --namespace efk

+ 安装filebeat
   helm pull elastic/filebeat
   tar -zvf filebeat  
  + helm install   filebeat   filebeat --namespace efk  

   + 安装kibana
     helm pull elastic/kibana
     tar -zvf kibana

     修改valuse.yaml的默认配置文件ingress未修改是默认5601

    + helm部署
     helm install   kibana   kibana --namespace logs
   