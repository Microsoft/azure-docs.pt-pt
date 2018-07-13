## <a name="scenario"></a>Cenário
Este documento irá guiá-implementação que utilize um endereço IP público estático, atribuído a uma máquina virtual (VM). Neste cenário, tem uma única VM com o seu próprio endereço IP público estático. A VM faz parte de uma sub-rede denominada **front-end** e também tem um endereço IP privado estático (**192.168.1.101**) nessa sub-rede.

Poderá ter um endereço IP estático para servidores web que necessitam de ligações de SSL no qual o certificado SSL está ligado a um endereço IP. 

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Pode seguir os passos abaixo para implementar o ambiente mostrado na figura acima.

