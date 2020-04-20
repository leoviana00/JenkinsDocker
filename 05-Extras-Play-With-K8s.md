Sua máquina é devagar como a minha?  
E que tal um **Laboratório Kubernetes** na nuvem, sem **pagar nada** por isso?  
   
Até parece aqueles *papinhos furados*! (risos)  
Senhoras e Senhores, é hora de atacar o [Play With Kubernetes](https://labs.play-with-k8s.com/), um PaaS que nos permite usufruir de um cluster Kubernetes num período de 4 horas.  
Após esse tempo o cluster se destrói, e será necessário iniciar os trabalhos novamente.  

Ah! Os pulos do gato! :cat:  
-  Para **Copiar e Colar** utilize `Ctrl + Insert` e `Shift + Insert`;  
-  Para colocar o terminal em **Tela Cheia** `Ctrl + Enter`;  
  

# Starting Master
1.  Clique em *Add New Instance* 
2.  Inicializar o nó master é tão tranquilo quanto:  
    `kubeadm init --apiserver-advertise-address $(hostname -i)`  
3.  Durante a inicialização, o nó master nos dará uma informação importante: a instrução **kubeadm join** contendo um token e um hash, **copie-a**!  
4.  Inicialize a rede do cluster:  
    `kubectl apply -n kube-system -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 |tr -d '\n')" ` 
5. Vamos acompanhar o nó master ficar em estado **Ready** (opcional)  
   `watch kubectl get nodes`
  
# Joining Slave
E tão simples quanto iniciar nosso nó master, é se unir a ele.  
1.  Clique em *Add New Instance*  
    *(Lembrando que agora teremos dois nós para navegarmos: `node1` e `node2`)*  
2.  Se lembra da instrução *join* do *kubeadm* que copiamos no item 3 do tópico anterior?  
    Hora do nosso novo nó, se tornar um recurso do nó principal, cole o comando aqui!  
    *(Algo como: kubeadm join --token <ID> <IP>:<PORTA> --discovery-token-ca-cert-hash sha256:<HASH>)*
3. Hora de acompanhar o nó slavel ficar em estado **Ready** (opcional)  
   Retorne ao nó master e: `watch kubectl get nodes`  
  
Com nosso cluster pronto, é hora de subirmos nosso Jenkins!  
Para isso basta executar os mesmos passos feitos via Minikube!  
