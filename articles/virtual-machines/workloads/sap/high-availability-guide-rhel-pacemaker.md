---
title: Configuração do Pacemaker no RHEL em Azure Microsoft Docs
description: Configuração do Pacemaker no Red Hat Enterprise Linux em Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: radeltch
ms.openlocfilehash: 21c551721815847eea4cb1435298ea6f7bf37966
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264482"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Configuração do Pacemaker no Red Hat Enterprise Linux em Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533,]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[SAP 1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Leia primeiro as seguintes Notas e papéis SAP:

* Nota SAP [1928533,]que tem:
  * A lista dos tamanhos de VM Azure que são suportados para a implementação de software SAP.
  * Informações importantes sobre a capacidade para tamanhos de VM Azure.
  * O software SAP suportado e o sistema operativo (OS) e as combinações de bases de dados.
  * A versão necessária para o kernel SAP necessário para Windows e Linux no Microsoft Azure.
* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* SAP Note [2002167] recomendou definições de OS para Red Hat Enterprise Linux
* SAP Nota [2009879] tem Diretrizes SAP HANA para Red Hat Enterprise Linux
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* A Nota [SAP 1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux (este artigo)][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [Replicação do sistema SAP HANA em cluster pacemaker](https://access.redhat.com/articles/3004101)
* Documentação Geral RHEL
  * [Visão geral de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração add-on de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência addon de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Políticas de Apoio a Clusters de Alta Disponibilidade RHEL - sbd e fence_sbd](https://access.redhat.com/articles/2800691)
* Documentação RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL - Máquinas Virtuais Microsoft Azure como Membros do Cluster](https://access.redhat.com/articles/3131341)
  * [Instalação e Configuração de um Cluster de Alta Disponibilidade do Red Hat Enterprise Linux 7.4 (e mais tarde) no Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Configure SAP S/4HANA ASCS/ERS com O Servidor Enfila Autónomo 2 (ENSA2) em Pacemaker em RHEL 7.6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Instalação de cluster

![Pacemaker na visão geral do RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> A Red Hat não suporta um cão de guarda emulado por software. O Chapéu Vermelho não suporta o SBD nas plataformas da nuvem. Para mais detalhes consulte Políticas de [Apoio para Clusters de Alta Disponibilidade RHEL - sbd e fence_sbd](https://access.redhat.com/articles/2800691).
> O único mecanismo de esgrima suportado para os clusters da Pacemaker Red Hat Enterprise Linux em Azure, é o agente da cerca azure.  

Os seguintes itens são pré-fixados com **[A]** - aplicável a todos os nós, **[1]** - apenas aplicável ao nó 1 ou **[2]** - apenas aplicável ao nó 2.

1. **[A]** Registo

   Registe as suas máquinas virtuais e prenda-as a uma piscina que contenha repositórios para rHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Note que ao anexar uma piscina a uma imagem Azure Marketplace PAYG RHEL, será efetivamente faturado duas vezes para o seu uso RHEL: uma para a imagem PAYG, e uma vez para o direito RHEL na piscina que anexa. Para mitigar isto, o Azure fornece agora imagens BYOS RHEL. Mais informações estão disponíveis [aqui.](../redhat/byos.md)

1. **[A]** Habilitar o RHEL para repos SAP

   Para instalar as embalagens necessárias, ative os seguintes repositórios.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Instalar add-on RHEL HA

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Recomendamos as seguintes versões do agente Da Vedação Azure (ou posteriormente) para que os clientes beneficiem de uma falha mais rápida ao longo do tempo, se uma paragem de recursos falhar ou os nós do cluster não puderem mais comunicar entre si:  
   > RHEL 7.6: cerca-agentes-4.2.1-11.el7_6.8  
   > RHEL 7.5: cerca-agentes-4.0.11-86.el7_5.8  
   > RHEL 7.4: cerca-agentes-4.0.11-66.el7_4.12  
   > Para mais informações, consulte [o Azure VM a funcionar como membro do cluster RHEL High Availability demora muito tempo a ser vedado, ou a esgrima falha/tempos antes do VM desligar](https://access.redhat.com/solutions/3408711).

   Verifique a versão do agente da cerca Azure. Se necessário, atualize-o para uma versão igual ou posterior à acima indicada.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Se precisar de atualizar o agente Da Vedação Azure e, se utilizar a função personalizada, certifique-se de atualizar a função personalizada para incluir o **powerOff**de ação . Para mais detalhes consulte [Criar um papel personalizado para o agente da cerca](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Configuração resolução de nome de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome de anfitrião nos seguintes comandos. A vantagem de utilizar /etc/hosts é que o seu cluster se torna independente de DNS, que também poderia ser um ponto único de falhas.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Alterar a palavra-passe do hacluster para a mesma senha

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Adicionar regras de firewall para pacemaker

   Adicione as seguintes regras de firewall a todas as comunicações de cluster entre os nós do cluster.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Permitir serviços básicos de cluster

   Executar os seguintes comandos para ativar o serviço Pacemaker e ligá-lo.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Criar cluster Pacemaker

   Executar os seguintes comandos para autenticar os nós e criar o cluster. Detete o símbolo para 30000 para permitir a manutenção da conservação da memória. Para mais informações, consulte [este artigo para linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
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
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Definir votos esperados

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Criar dispositivo STONITH

O dispositivo STONITH utiliza um Principal de serviço para autorizar com o Microsoft Azure. Siga estes passos para criar um Principal de serviço.

1. Ir para <https://portal.azure.com>
1. Abra o painel Azure Active Directory  
   Vá para propriedades e anote o ID de diretório. Esta é a identificação do **inquilino.**
1. Clique em registos de aplicações
1. Clique em Novo Registo
1. Insira um Nome, selecione "Contas apenas neste diretório de organização" 
2. Selecione Tipo de Aplicação Tipo "Web", introduza um URL de entrada (por exemplo, http:\//localhost) e clique em Adicionar  
   O URL de início de sessão não é utilizado e pode ser qualquer URL válido
1. Selecione Certificados e Segredos e, em seguida, clique em novo segredo de cliente
1. Introduza uma descrição para uma nova tecla, selecione "Nunca expira" e clique em Adicionar
1. Anote o valor. É usado como **palavra-passe** para o Diretor de Serviço
1. Selecione visão geral. Anote o ID da aplicação. É utilizado como nome de utilizador (ID de**login** nos passos abaixo) do Diretor de Assistência

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Criar um papel personalizado para o agente da cerca

O Principal de serviço não tem permissões para aceder aos seus recursos do Azure por predefinição. É necessário dar ao Diretor de Serviço permissões para iniciar e parar (desligar) todas as máquinas virtuais do cluster. Se ainda não criou o papel personalizado, pode criá-lo usando [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) ou [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)

Utilize o seguinte conteúdo para o ficheiro de entrada. Precisa adaptar o conteúdo para as suas subscrições, substitua c276fc76-9cd4-44c9-99a7-4fd71546436e e e91d47c4-76f3-4271-a796-21b4ecfe3624 com os Ids da sua subscrição. Se tiver apenas uma subscrição, remova a segunda entrada assignablescopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Atribuir o papel personalizado ao Diretor de Serviço

Atribua a função personalizada "Linux cerca agente de função" que foi criado no último capítulo para o Principal de serviço. Não utilize a função de proprietário mais!

1. Ir para https://portal.azure.com
1. Abra o painel de todos os recursos
1. Selecione a máquina virtual do primeiro nó de cluster
1. Clique em controle de acesso (IAM)
1. Clique em Adicionar atribuição de função
1. Selecione a função de "Função de agente de cerca de Linux"
1. Introduza o nome da aplicação que criou acima
1. Clicar em Guardar

Repita os passos acima para o segundo nó de cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Criar os dispositivos STONITH

Depois de editar as permissões para as máquinas virtuais, pode configurar os dispositivos STONITH no cluster.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Utilize o seguinte comando para configurar o dispositivo de vedação.

> [!NOTE]
> A opção 'pcmk_host_map' só é exigida no comando, se os nomes do anfitrião RHEL e os nomes do nó Azure não forem idênticos. Consulte a secção ousada no comando.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Permitir a utilização de um dispositivo STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha]
