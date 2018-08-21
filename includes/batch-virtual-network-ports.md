- A VNet tem de estar na mesma **região** e **subscrição** do Azure da conta do Batch.

- Para os conjuntos criados com uma configuração de máquina virtual, apenas são suportadas VNets baseadas no Azure Resource Manager. Para os conjuntos criados com uma configuração de serviços cloud, apenas são suportadas VNets clássicas.
  
- Para utilizar uma VNets clássica, o principal de serviço `MicrosoftAzureBatch` tem de ter a função `Classic Virtual Machine Contributor` do Controlo de Acesso Baseado em Funções (RBAC) na VNet especificada. Para utilizar uma VNet baseada no Azure Resource Manager, precisa de permissões para aceder à VNet e para implementar VMs na sub-rede.

- A sub-rede especificada para o conjunto deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o conjunto; ou seja, a soma de propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do conjunto. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o conjunto atribui parcialmente os nós de computação e ocorre um erro de redimensionamento. 

- Os agrupamentos na configuração da máquina virtual implementados numa VNet do Azure atribuem automaticamente os recursos de redes adicionais do Azure. Os recursos seguintes são necessários para cada 50 nós de agrupamento numa VNet: 1 grupo de segurança de rede, 1 endereço IP público e 1 balanceador de carga. Esses recursos estão limitados por [quotas](../articles/batch/batch-quota-limit.md) na subscrição que contém a rede virtual fornecida ao criar um agrupamento do Batch.

- A VNet tem de permitir a comunicação do serviço Batch para conseguir agendar tarefas nos nós de computação. Isto pode ser verificado ao confirmar se a VNet tem grupos de segurança de rede (NSGs) associados. Se a comunicação com os nós de computação na sub-rede especificada for recusada por um NSG, o serviço Batch define o estado dos nós de computação como **inutilizável**. 

- Se a VNet especificada tiver Grupos de Segurança de Rede (NSGs) associados e/ou uma firewall, configure as portas de entrada e saída conforme mostrado nas seguintes tabelas:


  |    Porta(s) de Destino    |    Endereço IP de origem      |   Porta de origem    |    O Batch adiciona NSGs?    |    Necessário para a VM seja utilizável?    |    Ação do utilizador   |
  |---------------------------|---------------------------|----------------------------|----------------------------|-------------------------------------|-----------------------|
  |   <ul><li>Em agrupamentos criados com a configuração de máquina virtual: 29876, 29877</li><li>Nos conjuntos criados com a configuração de serviço cloud: 10100, 20100, 30100, 30100</li></ul>        |    * <br /><br />Apesar de isto necessitar efetivamente de "permitir todos", o serviço de Batch aplica um NSG ao nível da interface de rede em cada VM criada sob a configuração da máquina virtual que filtra todos os endereços de IP de serviço não pertencente ao Batch. | * ou 443 |    Sim. O Batch adiciona NSGs ao nível das interfaces de rede (NIC) anexadas a VMs. Estes NSGs permitem tráfego apenas de endereços IP da função do serviço Batch. Mesmo que abra estas portas para a Internet, o tráfego é bloqueado no NIC. |    Sim  |  Não tem de especificar um NSG, porque o Batch só permite endereços IP do Batch. <br /><br /> No entanto, se especificar um NSG, certifique-se que estas portas estão abertas para tráfego de entrada.|
  |    3389 (Windows), 22 (Linux)               |    Máquinas de utilizador, utilizadas para fins de depuração, para que possa aceder remotamente à VM.    |   *  | Não                                    |    Não                    |    Adicione NSGs se quiser permitir o acesso remoto (RDP ou SSH) à VM.   |                                


  |    Porta(s) de Saída    |    Destino    |    O Batch adiciona NSGs?    |    Necessário para a VM seja utilizável?    |    Ação do utilizador    |
  |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
  |    443    |    Storage do Azure    |    Não    |    Sim    |    Se adicionar NSGs, confirme que esta porta está aberta ao tráfego de saída.    |

   Confirme também se o ponto final do Armazenamento do Azure pode ser resolvido por qualquer servidor DNS personalizado que sirva a sua VNet. Mais concretamente, devem ser resolvíveis os URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net`. 

   Se adicionar um Gestor de Recursos baseado em NSG, pode utilizar [etiquetas de serviço](../articles/virtual-network/security-overview.md#service-tags) para selecionar os endereços IP de Armazenamento para a região específica para ligações de saída. Tenha em atenção que os endereços IP de Armazenamento têm de ter a mesma região que a sua conta do Batch e da VNet. As etiquetas de serviço estão atualmente em pré-visualização nas regiões do Azure selecionadas.