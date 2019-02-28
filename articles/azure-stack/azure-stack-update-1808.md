---
title: O Azure Stack 1808 atualização | Documentos da Microsoft
description: Saiba mais sobre o que há de novo na atualização 1808 para os sistemas integrados do Azure Stack, incluindo os problemas conhecidos e onde pode transferir a atualização.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: b94d27e903fd01c3be029128a64c3e3369669bf7
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986154"
---
# <a name="azure-stack-1808-update"></a>Atualização de 1808 de pilha do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1808. O pacote de atualização inclui melhorias, correções e problemas conhecidos para esta versão do Azure Stack. Este artigo também inclui uma ligação para que possa transferir a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados com o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é apenas para sistemas integrados do Azure Stack. Não é aplicável este pacote de atualização para o Azure Stack Development Kit.

## <a name="build-reference"></a>Criar a referência

É o número de compilação de atualização do Azure Stack 1808 **1.1808.0.97**.  

### <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes aprimoramentos para o Azure Stack.

<!--  2682594   | IS  --> 
- **Todos os ambientes do Azure Stack agora utilizam o formato de fuso horário da hora Universal Coordenada (UTC).**  Todos os dados de registo e informações relacionadas agora apresentados no formato UTC. Se atualizar de uma versão anterior que não foi instalada com o UTC, o seu ambiente é atualizado para utilizar UTC. 

<!-- 2437250  | IS  ASDK --> 
- **Discos geridos são suportados.** Agora, pode utilizar discos geridos em máquinas de virtuais do Azure Stack e conjuntos de dimensionamento de máquina virtual. Para obter mais informações, consulte [Managed Disks do Azure Stack: Diferenças e considerações](/azure/azure-stack/user/azure-stack-managed-disk-considerations).

<!-- 2563799  | IS  ASDK --> 
- **O Azure Monitor**. Como o Azure Monitor, no Azure, o Azure Monitor no Azure Stack fornece registos e métricas de infraestrutura de nível de base para a maioria dos serviços. Para obter mais informações, consulte [do Azure Monitor no Azure Stack](/azure/azure-stack/user/azure-stack-metrics-azure-data).

<!-- 2487932| IS --> 
- **Preparar para o anfitrião de extensão**. Pode utilizar o anfitrião de extensão para o ajudar a proteger do Azure Stack, reduzindo o número de portas de TCP/IP necessárias. Com a atualização de 1808, pode preparar, prepare-se do Azure Stack para o anfitrião de extensão. Para obter mais informações, consulte [preparar para o anfitrião de extensão para o Azure Stack](/azure/azure-stack/azure-stack-extension-host-prepare).

<!-- IS --> 
- **Itens de galeria para conjuntos de dimensionamento de máquinas virtuais estão agora incorporados no**. O item de galeria o conjunto de dimensionamento de máquinas virtuais sejam agora disponibilizado nos portais do utilizador e administrador sem ter de transferi-lo.  Se atualizar para o 1808 está disponível após a conclusão da atualização.  

<!-- IS, ASDK --> 
- **O conjunto de dimensionamento de máquinas virtuais dimensionamento**. Pode utilizar o portal para [dimensionar um conjunto de dimensionamento de Máquina Virtual](azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

<!-- 2489570 | IS ASDK--> 
- **Suporte para configurações de política de IPSec/IKE personalizadas** para [gateways de VPN no Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

<!-- | IS ASDK--> 
- **Item do marketplace Kubernetes**. Agora, pode implementar clusters Kubernetes com o [item do mercado de Kubernetes](azure-stack-solution-template-kubernetes-cluster-add.md). Os utilizadores podem selecionar o item de Kubernetes e preencher alguns parâmetros para implementar um cluster de Kubernetes no Azure Stack. O objetivo dos modelos é simplificar para os utilizadores para configurar as implementações de Kubernetes de programador/teste em poucos passos.

<!-- | IS ASDK--> 
- **Modelos de Blockchain**. Agora, pode executar [implementações de consórcio Ethereum](user/azure-stack-ethereum.md) no Azure Stack. Encontrará três novos modelos no [modelos de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates). Eles permitem que o utilizador implementar e configurar uma rede Ethereum de consórcio com vários membros com o mínimo de conhecimento do Azure e Ethereum. O objetivo dos modelos é simplificar para os utilizadores para configurar as implementações de Blockchain de programador/teste em poucos passos.

<!-- | IS ASDK--> 
- **A API versão perfil 2017-03-09-profile foi atualizada para 2018-03-01-híbrido**. Perfis de API especificar o fornecedor de recursos do Azure e a versão de API para pontos finais REST do Azure. Para obter mais informações sobre os perfis, consulte [perfis de versão de API de gerir no Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles).

### <a name="fixed-issues"></a>Problemas corrigidos

<!-- IS ASDK--> 
- Foi corrigido o problema para a criação de um conjunto de disponibilidade no portal do que resultou no conjunto de um domínio de falha e o domínio de atualização de 1. 

<!-- IS ASDK --> 
- Definições para dimensionar conjuntos de dimensionamento de máquinas virtuais estão agora disponíveis no portal.  

<!-- 2494144- IS, ASDK --> 
- Agora é resolvido o problema que impediu que aparece ao selecionar um tamanho de VM para a implementação de alguns tamanhos de máquinas virtuais de série F. 

<!-- IS, ASDK --> 
- Melhorias de desempenho durante a criação de máquinas virtuais e muito mais otimizada a utilização de armazenamento subjacente.

- **Várias correções** de desempenho, estabilidade, segurança e o sistema operativo que é utilizado pelo Azure Stack.


### <a name="changes"></a>Alterações
<!-- 1697698  | IS, ASDK --> 
- *Tutoriais de início rápido* na ligação de agora de dashboard do portal de utilizador para os artigos relevantes na documentação do Azure Stack online.

<!-- 2515955   | IS ,ASDK--> 
- *Todos os serviços* substitui *mais serviços* nos portais do administrador e utilizador do Azure Stack. Agora, pode utilizar *todos os serviços* como uma alternativa para navegar nos portais do Azure Stack, da mesma forma que nos portais do Azure.

<!-- TBD | IS, ASDK --> 
- *+ Criar um recurso* substitui *+ novo* nos portais do administrador e utilizador do Azure Stack.  Agora, pode utilizar *+ criar um recurso* como uma alternativa para navegar nos portais do Azure Stack, da mesma forma que nos portais do Azure.  

<!--  TBD – IS, ASDK --> 
- *Básico A* tamanhos de máquinas virtuais foram extinguidos para [criar conjuntos de dimensionamento de máquinas virtuais](azure-stack-compute-add-scalesets.md) (VMSS) através do portal. Para criar um VMSS com este tamanho, utilize o PowerShell ou um modelo.  

### <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as seguintes atualizações:  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

Para obter mais informações sobre essas vulnerabilidades, clique nos links anteriores, ou consulte o artigo da Base de dados de conhecimento da Microsoft [4343887](https://support.microsoft.com/help/4343887).

Esta atualização também contém atenuação para a vulnerabilidade de canal de lado a execução especulativa conhecida como falha de Terminal de L1 (L1TF), descrito na [consultoria de segurança da Microsoft ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Pré-requisitos

- Instale a atualização do Azure Stack 1807 antes de aplicar a atualização do Azure Stack 1808. 

- Instale a atualização mais recente disponível ou a correção para a versão 1807.  
  > [!TIP]  
  > Subscrever o seguinte procedimento *RRS* ou *Atom* feeds para se manter atualizado com correções do Azure Stack:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...

- Antes de iniciar a instalação desta atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os parâmetros seguintes para validar o status do seu Azure Stack e resolver quaisquer problemas operacionais detectados, incluindo todos os avisos e falhas. Também rever alertas ativos e resolver qualquer um que requerem uma ação.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```

- Quando o Azure Stack é gerido pelo System Center Operations Manager (SCOM), certifique-se de que atualizar o pacote de gestão para o Microsoft Azure Stack para a versão 10.0.3.11 antes de aplicar 1808.

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando executa [AzureStack teste](azure-stack-diagnostic-test.md) após a atualização 1808, será apresentada uma mensagem de aviso do controlador de gestão de placas base (BMC). Pode ignorar este aviso.

<!-- 2468613 - IS --> 
- Durante a instalação desta atualização, poderá ver alertas com o título *erro – o modelo para typu FaultType UserAccounts.New está em falta.*  Pode ignorar com segurança estes alertas. Estes alertas serão fechado automaticamente depois de concluída a instalação desta atualização.

<!-- 2489559 - IS --> 
- Não tente criar máquinas virtuais durante a instalação desta atualização. Para obter mais informações sobre a gestão de atualizações, consulte [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md#plan-for-updates).

<!-- 2830461 - IS --> 
- Em determinadas circunstâncias quando uma atualização necessita de atenção, o alerta correspondente pode não ser gerado. O estado preciso ainda será refletido no portal e não é afetado.

### <a name="post-update-steps"></a>Passos de pós-atualização

> [!Important]  
> Prepare-se a implementação do Azure Stack para o anfitrião de extensão. Preparar o seu sistema usando as seguintes orientações [preparar para o anfitrião de extensão para o Azure Stack](azure-stack-extension-host-prepare.md).

Após a instalação desta atualização, instale as correções aplicáveis. Para obter mais informações, consulte os seguintes artigos da base de dados de conhecimento, bem como nossos [política de manutenção](azure-stack-servicing-policy.md). 
- [KB 4481066 – o Azure Stack correção Azure Stack correção 1.1808.9.117](https://support.microsoft.com/help/4481066/)


## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Seguem-se após a instalação problemas conhecidos para esta versão de compilação.

### <a name="portal"></a>Portal

- A documentação técnica do Azure Stack enfoca a versão mais recente do Azure Stack. Devido a alterações de portais entre versões, o que vê quando utilizar os portais do Azure Stack pode variar do que vê na documentação. 

<!-- TBD - IS ASDK --> 
- Poderá ver um dashboard em branco no portal. Para recuperar o dashboard, clique em **editar Dashboard**, em seguida, clique com botão direito e selecione **repor para o estado predefinido**.

<!-- 2930718 - IS ASDK --> 
- No portal do administrador, ao acessar os detalhes de qualquer subscrição do utilizador, depois de fechar o painel e clique em **recente**, não é apresentado o nome de subscrição de utilizador.

<!-- 3060156 - IS ASDK --> 
- O administrador e o utilizador portais, clicando em definições do portal e selecionando **eliminar todas as definições e dashboards privados** não funcionar conforme esperado. É apresentada uma notificação de erro. 

<!-- 2930799 - IS ASDK --> 
- O administrador e o utilizador portais, em **todos os serviços**, o elemento **planos de proteção DDoS** incorretamente está listado. Não é, na verdade, disponível no Azure Stack. Se tentar criá-lo, é apresentado um erro a indicar que o portal não foi possível criar o item do marketplace. 

<!-- 2930820 - IS ASDK --> 
- Nos portais do administrador e o utilizador, se pesquisar por "Docker", o item incorretamente é devolvido. Não é, na verdade, disponível no Azure Stack. Se tentar criá-lo, é apresentado um painel com uma indicação de erro. 

<!-- 2967387 – IS, ASDK --> 
- A conta que utiliza para iniciar sessão no portal de utilizador ou administrador do Azure Stack indicará **utilizador não identificado**. Isto ocorre quando a conta não tem qualquer um *primeira* ou *última* nome especificado. Para contornar este problema, edite a conta de utilizador para fornecer o primeiro ou último nome. Deve, em seguida, termine sessão e, em seguida, inicie sessão novamente para o portal. 

<!--  2873083 - IS ASDK --> 
-  Quando utiliza o portal para criar um dimensionamento de máquina virtual definido (VMSS), o *tamanho da instância* pendente não carregados corretamente ao utilizar o Internet Explorer. Para contornar este problema, use outro navegador ao utilizar o portal para criar um VMSS.  

<!-- 2931230 – IS  ASDK --> 
- Não não possível eliminar os planos que são adicionados a uma subscrição de utilizador como um plano de suplemento, mesmo quando remover o plano de subscrição de utilizador. O plano irá permanecer até que as subscrições que referenciam o plano de suplemento também são eliminadas. 

<!--2760466 – IS  ASDK --> 
- Quando instala um novo ambiente do Azure Stack que executar esta versão, o alerta que indica *ativação necessária* poderão não ser apresentados. [Ativação](azure-stack-registration.md) é necessária antes de poder utilizar a distribuição de mercado.  

<!-- TBD - IS ASDK --> 
- Os dois tipos de subscrição administrativas que foram introduzidos com versão 1804 não devem ser utilizados. Os tipos de subscrição são **medição subscrição**, e **subscrição de consumo**. Esses tipos de subscrição são visíveis no novos ambientes do Azure Stack a partir da versão 1804, mas ainda não estão prontos a utilizar. Deve continuar a utilizar o **fornecedor predefinido** tipo de subscrição.

<!-- TBD - IS ASDK --> 
- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos inteiro e, em seguida, eliminar subscrições de utilizador.

<!-- TBD - IS ASDK --> 
- Não é possível ver as permissões à sua subscrição a utilizar os portais do Azure Stack. Como solução, utilize o PowerShell para verificar as permissões.


### <a name="health-and-monitoring"></a>Estado de funcionamento e monitorização

<!-- TBD - IS -->
- Poderá ver os seguintes alertas repetidamente aparecer e desaparecer, em seguida, no seu sistema do Azure Stack:
   - *Instância de função de infraestrutura indisponível*
   - *Nó de unidade de escala está offline*
   
  Volte a executar o [AzureStack teste](azure-stack-diagnostic-test.md) cmdlet para verificar o estado de funcionamento das instâncias de função de infraestrutura e dimensionar nós de unidade. Se não existem problemas são detetados pelo [AzureStack teste](azure-stack-diagnostic-test.md), pode ignorar estes alertas. Se for detetado um problema, pode tentar iniciar a instância de função de infraestrutura ou o nó com o portal de administração ou PowerShell.

  Este problema é resolvido da versão mais recente [versão de correção de 1808](https://support.microsoft.com/help/4481066/), por isso, certifique-se de que instale esta correção, se estiver a ter o problema.

<!-- 1264761 - IS ASDK --> 
- Poderá ver alertas para o **controlador de estado de funcionamento** componente que tem os seguintes detalhes:  

   #1 do alerta:
   - NOME:  Função de infraestrutura mau estado de funcionamento
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de estado de funcionamento
   - DESCRIÇÃO: O Scanner de Heartbeat do controlador de estado de funcionamento não está disponível. Isto pode afetar as métricas e relatórios de estado de funcionamento.  

  Alerta #2:
   - NOME:  Função de infraestrutura mau estado de funcionamento
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de estado de funcionamento
   - DESCRIÇÃO: O Scanner de falhas de controlador do Estado de funcionamento não está disponível. Isto pode afetar as métricas e relatórios de estado de funcionamento.

  Ambos os alertas podem ser ignoradas com segurança e vai ser fechado automaticamente ao longo do tempo.  


<!-- 2812138 | IS --> 
- Poderá ver um alerta para **armazenamento** componente que contém os seguintes detalhes:

   - NOME: Erro de comunicação interno do serviço de armazenamento  
   - GRAVIDADE: Crítica  
   - COMPONENTE: Armazenamento  
   - DESCRIÇÃO: Ocorreu um erro de comunicação interna do serviço de armazenamento quando enviar pedidos para os seguintes nós.  

    O alerta pode ser ignorado com segurança, mas terá de fechar o alerta manualmente.

<!-- 2368581 - IS. ASDK --> 
- Um operador do Azure Stack, se receber um alerta de pouca memória e máquinas de virtuais de inquilino não for possível implementar com um **erro de criação de recursos de infraestrutura de VM**, é possível que o carimbo de data / Azure Stack está disponível memória esgotada. Utilize o [Planeador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para melhor compreender a capacidade disponível para as cargas de trabalho.

### <a name="compute"></a>Computação

- Ao criar um [VM da série Dv2](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), D11 14v2 VMs permitem-lhe criar 4, 8, 16 e discos de dados de 32, respetivamente. No entanto, o painel VM de criar mostra 8, 16, 32 e 64 discos de dados.

<!-- 3164607 – IS, ASDK -->
- Reattaching um disco desligado para a mesma máquina virtual (VM) com o mesmo nome e o LUN falha com um erro **não é possível anexar o disco de dados 'datadisk"a VM"vm1"**. O erro ocorre porque o disco está atualmente a ser desligado ou o último desanexar falha na operação. Aguarde até que o disco é completamente desanexado e, em seguida, tente novamente ou elimine/desanexe o disco explicitamente novamente. A solução alternativa é voltar a ligá-lo com um nome diferente ou num LUN diferente. 

<!-- 3099544 – IS, ASDK --> 
- Quando cria uma nova VM com o portal do Azure Stack, e selecionar o tamanho da VM, a coluna USD/mês é apresentada com um **indisponível** mensagem. Esta coluna não deve aparecer; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 3090289 – IS, ASDK --> 
- Depois de aplicar o 1808 de atualização, pode encontrar os seguintes problemas quando implementar VMs com discos geridos:

   1. Se a subscrição tiver sido criada antes da atualização de 1808, implantação de VM com Managed Disks poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos para cada subscrição:
      1. No portal do inquilino, aceda a **subscrições** e encontrar a subscrição. Clique em **fornecedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **voltar a registar**.
      2. Na mesma subscrição, aceda a **controlo de acesso (IAM)** e certifique-se de que o **AzureStack-DiskRP-Client** função está listada.
   2. Se tiver configurado o ambiente multi-inquilino, a implementação de VMs numa assinatura associada um diretório de convidado poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos:
      1. Aplicar a [correção de pilha do 1808 Azure](https://support.microsoft.com/help/4481066/).
      2. Siga os passos em [este artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) reconfigurar cada um dos seus diretórios de convidado.
      
<!-- 3179561 - IS --> 
- Utilização de discos gerida é comunicada nas horas, conforme descrito no [FAQ de utilização do Azure Stack](azure-stack-usage-related-faq.md#managed-disks). No entanto, a faturação do Azure Stack utiliza o preço mensal em vez disso, para que pode obter incorretamente cobrada a utilização de Managed Disks em ou antes de 27 de Setembro. Podemos ter temporariamente suspensa custos para os Managed Disks após 27 de Setembro até que o problema de faturação é resolvido. Se ter sido cobrados incorretamente para a utilização de Managed Disks, contacte o suporte de faturação da Microsoft.
Relatórios de utilização produzidos a partir de APIs de utilização do Azure Stack mostram quantidades corretas e podem ser utilizados.

<!-- 3507629 - IS, ASDK --> 
- Discos geridos cria dois novos [tipos de quota de computação](azure-stack-quota-types.md#compute-quota-types) para limitar a capacidade máxima de discos geridos que podem ser aprovisionados. Por predefinição, 2048 GiB é alocado para cada tipo de quota de discos geridos. No entanto, pode encontrar os seguintes problemas:

   - Para quotas criadas antes da atualização de 1808, a quota de Managed Disks mostrará 0 valor no portal do administrador, embora 2048 GiB é alocado. Pode aumentar ou diminuir o valor com base nas suas necessidades reais e recentemente defina o valor da quota substitui a predefinição do 2048 GiB.
   - Se atualizar o valor da quota como 0, é equivalente para o valor predefinido de 2048 GiB. Como solução, defina o valor de quota para 1.

<!-- 2869209 – IS, ASDK --> 
- Ao utilizar o [ **Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), tem de utilizar o **- OsUri** parâmetro como a conta de armazenamento URI onde o disco é carregado. Se utilizar o caminho local do disco, o cmdlet falhar com o seguinte erro: *Operação de longa execução falhou com o estado 'Com falhas'*. 

<!--  2966665 – IS, ASDK --> 
- SSD de anexar discos de dados para o tamanho de premium geridos disco máquinas virtuais (DS, DSv2, Fs, Fs_V2) falha com um erro:  *Falha ao atualizar discos para a máquina virtual "vmname" erro: Pedido não é possível efetuar a operação porque o tipo de conta de armazenamento "Premium_LRS" não é suportado para o tamanho da VM "Standard_DS/Ds_V2/FS/Fs_v2)*

   Para contornar este problema, utilize *Standard_LRS* discos de dados em vez de *Premium_LRS discos*. Usar *Standard_LRS* discos de dados não é alterado IOPs ou o custo de faturação. 

<!--  2795678 – IS, ASDK --> 
- Ao utilizar o portal para criar máquinas virtuais (VM) num tamanho VM premium (DS, Ds_v2, FS, FSv2), a VM é criada numa conta de armazenamento standard. Criação de uma conta de armazenamento standard não afeta funcionalmente, IOPs, ou de faturação. 

   Pode ignorar o aviso que diz: *Que escolheu utilizar um disco padrão num tamanho que suporte discos premium. Isso pode afetar o desempenho do sistema operativo e não é recomendado. Considere utilizar o armazenamento premium (SSD) em vez disso.*

<!-- 2967447 - IS, ASDK --> 
- Experiência de criar o conjunto de dimensionamento de máquinas virtuais (VMSS) fornece baseada em CentOS 7.2 como uma opção para implementação. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para a sua implementação ou utilizar um modelo do Azure Resource Manager, especificando outra imagem do CentOS que foi transferida pela operadora de rede antes da implantação do marketplace.  

<!-- 2724873 - IS --> 
- Ao utilizar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** para gerir unidades de escala, a primeira tentativa para iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução deve ser bem-sucedida para concluir a operação. 

<!-- TBD - IS ASDK --> 
- Ao criar máquinas virtuais no portal de utilizador do Azure Stack, o portal apresenta um número incorreto de discos de dados que pode anexar a uma VM da série DS. Séries de DS VMs capaz de acomodar os discos de dados como a configuração do Azure.

<!-- TBD - IS ASDK --> 
- Se uma extensão numa implantação de VM o aprovisionamento demora demasiado tempo, os utilizadores devem permitir que o limite de tempo de aprovisionamento em vez de tentar interromper o processo para desaloque ou elimine a VM.  

<!-- 1662991 IS ASDK --> 
- Diagnóstico de VM do Linux não é suportado no Azure Stack. Quando implementa uma VM do Linux com o diagnóstico VM ativado, a implementação falhar. A implementação falhar, também, se ativar as métricas básicas de VM do Linux através das definições de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Quando registra o **Microsoft.Insight** fornecedor de recursos nas definições de subscrição e criar uma VM do Windows com o convidado SO diagnóstico ativado, o gráfico de percentagem de CPU na página de descrição geral de VM não será capaz de mostrar dados métricos.

   Para localizar o gráfico de percentagem de CPU para a VM, vá para o **métricas** métricas de convidado de painel e mostrar todas as VMS suportadas do Windows.

- Uma VM do Ubuntu 18.04 criado com autorização de SSH ativada não permitirá que use as chaves SSH para iniciar sessão. Como solução, utilize o acesso VM para a extensão do Linux para implementar as chaves SSH após o aprovisionamento ou utilizar autenticação baseada em palavra-passe.

### <a name="networking"></a>Redes  

<!-- 1766332 - IS ASDK --> 
- Sob **Networking**, se clicar em **criar Gateway de VPN** para configurar uma ligação de VPN, **baseado em política** está listado como um tipo de VPN. Não selecione esta opção. Apenas os **rota com base** opção é suportada no Azure Stack.

<!-- 1902460 - IS ASDK --> 
- O Azure Stack oferece suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as subscrições de inquilino. Após a criação da primeira rede local ligação de gateway, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

<!-- 16309153 - IS ASDK --> 
- Numa rede Virtual que foi criado com uma definição de servidor DNS de *automática*, a mudança para uma falha de servidor DNS personalizado. As definições atualizadas não são enviadas para as VMs nessa vnet.

<!-- 2702741 -  IS ASDK --> 
- IPs públicos, que são implementadas utilizando o método alocação dinâmica não é garantido ser preservadas quando uma paragem da Desalocação é emitido.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *segredo rotação*, existe um período em que os endereços IP públicos estão inacessíveis para dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
- Em cenários em que o inquilino está a aceder a suas máquinas virtuais utilizando um túnel S2S VPN, poderão surgir um cenário em que as tentativas de ligação falharem se a sub-rede no local foi adicionada ao Gateway de rede Local já foi criado o gateway. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>Serviço de Aplicações

<!-- 2352906 - IS ASDK --> 
- Os utilizadores tem de registar o fornecedor de recursos de armazenamento antes de ser criar sua primeira função do Azure na subscrição.

<!-- 2489178 - IS ASDK --> 
- Para ampliar a infraestrutura (funções de trabalho, gerenciamento, funções de front-end), tem de utilizar o PowerShell, tal como descrito nas notas de versão para computação.



### <a name="usage"></a>Utilização  
<!-- TBD - IS ASDK --> 
- Dados de medidor de utilização de endereços de IP público de utilização mostram o mesmo *EventDateTime* valor para cada registo, em vez do *TimeDate* carimbo que mostra quando o registo foi criado. Atualmente, não pode utilizar estes dados para efetuar gestão de contas preciso de utilização de endereços IP pública.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização do Azure Stack 1808 partir [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários de ligação apenas, implementações do Azure Stack verificar periodicamente um ponto de extremidade seguro e notificam automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [gerir atualizações para o Azure Stack](azure-stack-updates.md).

## <a name="next-steps"></a>Passos Seguintes
- Para rever a política de manutenção para sistemas integrados do Azure Stack e o que deve fazer para manter seu sistema num estado suportado, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para utilizar o ponto final com privilégios (PEP) para monitorizar e retomar as atualizações, consulte [monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios](azure-stack-monitor-update.md).  
- Para uma descrição geral da gestão de atualizações no Azure Stack, veja [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar as atualizações com o Azure Stack, veja [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).  
