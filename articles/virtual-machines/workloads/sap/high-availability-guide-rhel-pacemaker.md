---
title: Criação do Pacemaker na RHEL em Azure | Microsoft Docs
description: Configuração do Pacemaker na Red Hat Enterprise Linux em Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2021
ms.author: radeltch
ms.openlocfilehash: af8523486b42af8c0722a56bdd813d6449692c14
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676879"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Configuração do Pacemaker na Red Hat Enterprise Linux em Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Leia primeiro as seguintes notas e artigos SAP:

* Nota SAP [1928533], que tem:
  * A lista de tamanhos Azure VM que são suportados para a implementação de software SAP.
  * Informações importantes sobre a capacidade dos tamanhos Azure VM.
  * O software SAP suportado, e as combinações de sistema operativo (OS) e base de dados.
  * A versão necessária para o kernel SAP para Windows e Linux no Microsoft Azure.
* O SAP Note [2015553] lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* SAP Note [2002167] recomendou definições de SO para Red Hat Enterprise Linux
* SAP Note [2009879] tem orientações SAP HANA para Red Hat Enterprise Linux
* A Nota [SAP 2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* Sap Note [2191498] tem a versão necessária do Agente anfitrião SAP para Linux em Azure.
* Sap Nota [2243692] tem informações sobre o licenciamento SAP em Linux em Azure.
* Sap Nota [1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização melhorada de Azure para o SAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Azure Virtual Machines planejamento e implementação para SAP em Linux][planning-guide]
* [Azure Virtual Machines implantação para SAP em Linux (este artigo)][deployment-guide]
* [Azure Virtual Machines DBMS implantação para SAP em Linux][dbms-guide]
* [Replicação do sistema SAP HANA no cluster pacemaker](https://access.redhat.com/articles/3004101)
* Documentação geral do RHEL
  * [Visão geral Add-On de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Alta Disponibilidade Add-On Administração](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de Add-On de Alta Disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Políticas de apoio para clusters de alta disponibilidade RHEL - sbd e fence_sbd](https://access.redhat.com/articles/2800691)
* Documentação RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL - Microsoft Azure Virtual Machines como Membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalar e Configurar um Red Hat Enterprise Linux 7.4 (e mais tarde) High-Availability Cluster no Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Considerações na adoção do RHEL 8 - Alta disponibilidade e clusters](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [Configure SAP S/4HANA ASCS/ERS com Standalone Enqueue Server 2 (ENSA2) em Pacemaker em RHEL 7.6](https://access.redhat.com/articles/3974941)
  * [RHEL para ofertas SAP em Azure](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Instalação de cluster

![Pacemaker na visão geral da RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> A Red Hat não suporta cães de guarda emulados por software. O Red Hat não suporta SBD nas plataformas de nuvem. Para mais detalhes consulte [políticas de suporte para clusters de alta disponibilidade RHEL - sbd e fence_sbd](https://access.redhat.com/articles/2800691).
> O único mecanismo de esgrima suportado para os clusters Pacemaker Red Hat Enterprise Linux em Azure, é o agente da cerca de Azure.  

Os seguintes itens são prefixados com **ambos [A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicáveis ao nó 2.

1. **[A]** Registar-se. Este passo não é necessário, se utilizar imagens ativadas pelo RHEL SAP HA.  

   Registe as suas máquinas virtuais e prenda-as a uma piscina que contenha repositórios para o RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Ao anexar uma piscina a uma imagem Azure Marketplace PAYG RHEL, você será efetivamente cobrado duas vezes para o seu uso RHEL: uma para a imagem PAYG, e uma para o direito RHEL na piscina que anexa. Para mitigar isto, o Azure fornece agora imagens BYOS RHEL. Poderá encontrar mais informações [aqui](../redhat/byos.md).  

1. **[A]** Permitir RHEL para repos SAP. Este passo não é necessário, se utilizar imagens ativadas pelo RHEL SAP HA.  

   Para instalar as embalagens necessárias, ative os seguintes repositórios.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Instalar a RHEL HA Add-On

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Recomendamos as seguintes versões do agente da Azure Fence (ou posteriormente) para que os clientes beneficiem de um tempo de execução mais rápido, se uma paragem de recursos falhar ou os nós de cluster não puderem mais comunicar quais:  
   > UTILIZAÇÃO RHEL 7.7 ou superior utiliza a versão mais recente disponível do pacote de agentes de vedação  
   > RHEL 7.6: cerca-agentes-4.2.1-11.el7_6.8  
   > RHEL 7.5: cerca-agentes-4.0.11-86.el7_5.8  
   > RHEL 7.4: cerca-agentes-4.0.11-66.el7_4.12  
   > Para obter mais informações, consulte [o Azure VM a funcionar como um membro do cluster RHEL High Availability demorou muito tempo a ser vedado, ou a esgrima falha/horários antes que o VM desligue.](https://access.redhat.com/solutions/3408711)

   Verifique a versão do agente da cerca Azure. Se necessário, atualize-a para uma versão igual ou posterior à acima indicada.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Se precisar de atualizar o agente da Cerca Azure e, se utilizar a função personalizada, certifique-se de atualizar a função personalizada para incluir o **powerOff de** ação . Para mais detalhes, [crie um papel personalizado para o agente da cerca.](#1-create-a-custom-role-for-the-fence-agent)  

1. **[A]** Resolução do nome do anfitrião da configuração

   Pode utilizar um servidor DNS ou modificar os /etc/anfitriões em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos.  

   >[!IMPORTANT]
   > Se utilizar nomes de anfitriões na configuração do cluster, é vital ter uma resolução fiável do nome do anfitrião. A comunicação do cluster falhará, se os nomes não estiverem disponíveis e isso pode levar a atrasos de falha no cluster.
   > O benefício da utilização /etc/anfitriões é que o seu cluster se torna independente do DNS, o que pode ser um único ponto de falhas também.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas para /etc/anfitriões. Altere o endereço IP e o nome de anfitrião para combinar com o seu ambiente

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Alterar a palavra-passe hacluster para a mesma senha

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Adicionar regras de firewall para pacemaker

   Adicione as seguintes regras de firewall a toda a comunicação de cluster entre os nós do cluster.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Permitir serviços básicos de cluster

   Executar os seguintes comandos para ativar o serviço Pacemaker e iniciá-lo.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Criar cluster pacemaker

   Executar os seguintes comandos para autenticar os nós e criar o cluster. Desloque o token a 30000 para permitir a manutenção da conservação da memória. Para mais informações, consulte [este artigo para Linux.][virtual-machines-linux-maintenance]  
   
   Se construir um cluster em **RHEL 7.x,** utilize os seguintes comandos:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Se construir um cluster em **RHEL 8.X,** utilize os seguintes comandos:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Verifique o estado do cluster, executando o seguinte comando:  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Definir votos esperados. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Se construir um aglomerado multi-nó, que é aglomerado com mais de dois nós, não coloque os votos em 2.    

1. **[1]** Permitir ações de cerca simultâneas

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>Criar dispositivo STONITH

O dispositivo STONITH utiliza um Diretor de Serviço para autorizar contra o Microsoft Azure. Siga estes passos para criar um Diretor de Serviço.

1. Ir para <https://portal.azure.com>
1. Abra a lâmina do Diretório Ativo Azure  
   Vá ao Properties e tome nota da identificação do Diretório. Esta é a identificação do **inquilino.**
1. Clique nos registos da App
1. Clique em Novo Registo
1. Insira um Nome, selecione "Contas apenas neste diretório de organização" 
2. Selecione o Tipo de Aplicação "Web", introduza um URL de entrada de sinal (por exemplo http: \/ /localhost) e clique em Adicionar  
   O URL de inscrição não é usado e pode ser qualquer URL válido
1. Selecione Certificados e Segredos e, em seguida, clique em Novo segredo de cliente
1. Introduza uma descrição para uma nova chave, selecione "Nunca expira" e clique em Adicionar
1. Faça um nó o Valor. É usado como **a palavra-passe** para o diretor de serviço
1. Selecione Descrição geral. Tome nota do ID do formulário de aplicação. É usado como nome de utilizador **(ID de login** nos passos abaixo) do Diretor de Serviço

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Criar um papel personalizado para o agente da cerca

O Diretor de Serviço não tem permissões para aceder aos seus recursos Azure por padrão. Você precisa dar ao Service Principal permissões para iniciar e parar (desligar) todas as máquinas virtuais do cluster. Se ainda não criou o papel personalizado, pode criá-lo utilizando [o PowerShell](../../../role-based-access-control/role-assignments-powershell.md) ou [o Azure CLI](../../../role-based-access-control/role-assignments-cli.md)

Utilize o seguinte conteúdo para o ficheiro de entrada. É necessário adaptar o conteúdo às suas subscrições, ou seja, substituir c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 com os IDs da sua assinatura. Se tiver apenas uma subscrição, remova a segunda entrada em 'AtribuableScopes'.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Atribuir o papel personalizado ao Diretor de Serviços

Atribua o papel personalizado "Linux Fence Agent Role" que foi criado no último capítulo ao Diretor de Serviço. Não utilize mais a função Proprietário!

1. Ir para https://portal.azure.com
1. Abra a lâmina de todos os recursos
1. Selecione a máquina virtual do primeiro nó de cluster
1. Clique no controlo de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione o papel "Linux Fence Agent Role"
1. Insira o nome da aplicação que criou acima
1. Clicar em Guardar

Repita os passos acima para o segundo nó de cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Criar os dispositivos STONITH

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> A opção 'pcmk_host_map' só é necessária no comando, se os nomes dos anfitriões RHEL e os nomes VM do Azure não forem idênticos. Especificar o mapeamento no nome de anfitrião do **formato:vm-name**.
> Consulte a secção arrojada no comando. Para obter mais informações, o [formato que devo utilizar para especificar mapeamentos de nó para dispositivos de atonith em pcmk_host_map](https://access.redhat.com/solutions/2619961)

Para o RHEL **7.X,** utilize o seguinte comando para configurar o dispositivo da cerca:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

Para o RHEL **8.X,** utilize o seguinte comando para configurar o dispositivo da cerca:  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> As operações de monitorização e esgrima são des serializadas. Como resultado, se houver uma operação de monitorização mais longa e evento de esgrima simultânea, não há atrasos no cluster failover, devido à operação de monitorização já em funcionamento.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Permitir a utilização de um dispositivo STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>O Agente de Cercas Azure requer conectividade de saída para pontos finais públicos como documentado, juntamente com possíveis soluções, na [conectividade de ponto final público para VMs utilizando iLB padrão](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
