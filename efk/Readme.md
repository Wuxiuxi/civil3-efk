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

   修改配置vi values.yaml
   filebeatConfig:
  filebeat.yml: |
    filebeat.config:
       modules:
         path: ${path.config}/modules.d/*.yml
         reload.enabled: true
    filebeat.autodiscover:
      providers:
        - type: kubernetes
          hints.enabled: true
          templates:
            - condition:
                equals:
                  # 堆栈对行日志出现的名称空间为baas
                  kubernetes.namespace: baas
              config:
                - type: docker
                  containers.ids:
                    - "${data.kubernetes.container.id}"
                  # 配置堆栈多行匹配规则
                  multiline:
                    pattern: '^[[:space:]]+(at|\.{3})\b|^Caused by:'
                    negate: false
                    match: after
            - condition:
                equals:
                  kubernetes.namespace: kube-system
              config:
                - type: docker
                  containers.ids:
                    - "${data.kubernetes.container.id}"
    output.elasticsearch:
      host: '${NODE_NAME}'
      hosts: '${ELASTICSEARCH_HOSTS:elasticsearch-master:9200}'
  
  + helm install   filebeat   filebeat --namespace efk  

   + 安装kibana
     helm pull elastic/kibana
     tar -zvf kibana

     修改valuse.yaml的默认配置文件ingress未修改是默认5601

    + helm部署
     helm install   kibana   kibana --namespace logs
   