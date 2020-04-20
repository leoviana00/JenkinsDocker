Agora, vocês devem escolher como pretender executar o **Kubernetes** (muito provavelmente vocês já tenham isso), eu optei pelo **Minikube**!  
Fiquem a vontade para escolher, e, caso tenham dúvida, dêem uma olhada na **Seção Extra** dexei para vocês overview sobre o **Play With K8s** para ajudar com essa 'dúvida'.   
  
# Cluster HandsOn
## Service
Hora de criar nosso *Service*, responsável por expor nosso endpoint, sem isso nosso *POD*, gerado pelo *Deployment*, nunca seria acessado:  
1.  `vi service.yml`  
2.  Copie o conteúdo do arquivo *jenkins-service.yml* disponibilizado na aula  
3.  `kubectl apply -f service.yml` 
4.  Verifique o serviço `kubectl get services` 

## Deployment
Hora de criar nosso *deployment*, responsável por orientar o kubernetes de como se devem ser criados os *PODs*:  
1.  `vi deployment.yml`  
2.  Copie o conteúdo do arquivo *jenkins-deployment.yml* disponibilizado na aula  
3.  Atualize a ENV **KUBERNETES_SERVER_URL** com o ip do seu Cluster: `kubectl cluster-info | grep master`  
5.  `kubectl apply -f deployment.yml` 
6.  Verifique o serviço `kubectl get deployments` 

Show! O serviço foi exposto e já podemos acessá-lo!  

## Role
Para provar que esse laboratório não deixa nenhuma feature para trás, antes de ver funcionar, veremos **falhar**! :cry:  
Navegue até as Configurações do Jenkins, na seção **Clouds** iremos testar a configuração.  
Falhou! Agora aplique a rule:    
1.  `vi rule.yml`  
2.  Copie o conteúdo do arquivo *jenkins-role.yml* disponibilizado na aula  
3.  `kubectl apply -f rule.yml`  
Vamos testar novamente e... `#SUCESSO`!   

## POD
Por último, Acesse Configurações Globais > Seção Cloud e atualize a URL Jenkins com o ip do POD, para isso execute:    
`kubectl get pods | grep ^jenkins`  
Obtenha o POD ID e:  
`kubectl describe pod <POD-ID> | grep IP`  
Para recuperar o IP do POD e atualizar o campo URL do Jenkins.    
  
Hora de ver os pipelines em execução!  
