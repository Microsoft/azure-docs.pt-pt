---
title: Atualização de 1809 de pilha do Azure | Documentos da Microsoft
description: Saiba mais sobre o que há de novo na atualização 1809 para os sistemas integrados do Azure Stack, incluindo os problemas conhecidos e onde pode transferir a atualização.
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
ms.openlocfilehash: 251f61a0356f4e8f379c17b477e24a0cefce68e7
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985007"
---
# <a name="azure-stack-1809-update"></a>Atualização de 1809 de pilha do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1809. O pacote de atualização inclui melhorias, correções e problemas conhecidos para esta versão do Azure Stack. Este artigo também inclui uma ligação para que possa transferir a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados com o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é apenas para sistemas integrados do Azure Stack. Não é aplicável este pacote de atualização para o Azure Stack Development Kit.

## <a name="build-reference"></a>Criar a referência

É o número de compilação de atualização do Azure Stack 1809 **1.1809.0.90**.  

### <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes aprimoramentos para o Azure Stack:

- Com esta versão, configurações de suporta sistemas de 4 a 16 nós integrado do Azure Stack. Pode utilizar o [Planeador de capacidade do Azure Stack](https://aka.ms/azstackcapacityplanner) para ajudar no seu planeamento da capacidade do Azure Stack e a configuração.

- <!--  2712869   | IS  ASDK -->  **Cliente de syslog do Azure Stack (disponibilidade geral)** este cliente permite que o reencaminhamento de auditorias, alertas e registos de segurança relacionados com a infraestrutura do Azure Stack para um syslog segurança ou de servidor de informações e eventos (SIEM) software de gestão externamente em relação ao Azure Stack. Agora, o cliente de syslog suporta especificando a porta em que o servidor syslog está a escutar.

   Com esta versão, o cliente de syslog está disponível em geral e pode ser usado em ambientes de produção.

   Para obter mais informações, consulte [reencaminhamento do Azure Stack syslog](azure-stack-integrate-security.md).

- Agora, pode [mova o recurso de Registro](azure-stack-registration.md#move-a-registration-resource) no Azure entre grupos de recursos sem ter de voltar a registar. Fornecedores de soluções cloud (CSPs) também pode mover o recurso de registro entre subscrições, desde que ambas as subscrições novas e antigas são mapeadas para o mesmo ID de parceiro CSP. Isto não afeta os mapeamentos de inquilino de cliente existente. 

- Foi adicionado suporte para atribuir vários endereços IP por interface de rede.  Para obter mais detalhes, consulte [atribuir vários endereços IP para máquinas virtuais com o PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell).

### <a name="fixed-issues"></a>Problemas corrigidos

<!-- TBD - IS ASDK -->
- No portal, o gráfico de memória livre/utilizado capacidade de geração de relatórios agora é exato. Pode agora mais fiável prever o número de VMs que é capazes de criar.

<!-- TBD - IS ASDK --> 
- Foi corrigido um problema em que criou as máquinas virtuais no portal de utilizador do Azure Stack e o portal apresentado um número incorreto de discos de dados que pode anexar a uma VM da série DS. Séries de DS VMs capaz de acomodar os discos de dados como a configuração do Azure.

- Os seguintes problemas de disco gerido foram corrigidos na 1809 e também foram corrigidos no 1808 [do Azure Stack correção 1.1808.9.117](https://support.microsoft.com/help/4481066/): 

   <!--  2966665 – IS, ASDK --> 
   - Foi corrigido o problema em que anexar discos de dados SSD para máquinas de virtuais de disco gerido (DS, DSv2, Fs, Fs_V2) falhou com um erro de tamanho de premium:  *Falha ao atualizar discos para a máquina virtual "vmname" erro: Pedido não é possível efetuar a operação porque o tipo de conta de armazenamento "Premium_LRS" não é suportado para o tamanho da VM "Standard_DS/Ds_V2/FS/Fs_v2)*. 
   
   - Criação de um disco gerido de VM usando **createOption**: **Anexar** falha com o erro seguinte: *Falha na operação de longa com o estado "Falhou". Informações adicionais: "Ocorreu um erro de execução interna."*
   Código de erro: InternalExecutionError ErrorMessage: Ocorreu um erro de execução interno.
   
   Este problema agora foi corrigido.

- <!-- 2702741 -  IS, ASDK --> Foi corrigido o problema no qual os IPs públicos que foram implementadas utilizando a alocação de dinâmica método não eram a garantia de ser preservadas quando uma paragem da Desalocação é emitido. Agora são preservadas.

- <!-- 3078022 - IS, ASDK --> Se uma VM foi parada-desalocada antes de 1808 não pode ser reatribuída após a atualização 1808.  Este problema é resolvido de 1809. Instâncias que foram neste estado e não foi possível iniciar podem ser iniciadas na 1809 com esta correção. A correção também evita que este problema ocorrer.

### <a name="changes"></a>Alterações

<!-- 2635202 - IS, ASDK -->
- Serviço de cópia de segurança de infra-estrutura move-se do [rede de infraestrutura públicas](https://docs.microsoft.com/azure/azure-stack/azure-stack-network) para o [rede VIP pública](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network). Os clientes terão garantir que o serviço tem acesso a localização de armazenamento de cópia de segurança da rede VIP pública.  

> [!IMPORTANT]  
> Se tiver uma firewall que não permite ligações a partir da rede VIP pública para o servidor de ficheiros, esta alteração fará com que as cópias de segurança de infra-estrutura efetuar a ativação com "Erro 53 o caminho de rede não foi encontrado". Esta é uma alteração de última hora que tenha não existe solução razoável. Com base nos comentários dos clientes, Microsoft irá reverter esta alteração numa correção. Consulte a [publicar a secção de passos de atualização](#post-update-steps) para obter mais informações sobre correções disponíveis para 1809. Assim que a correção está disponível, certifique-se para aplicá-la depois de atualizar para 1809 apenas se as políticas de rede não permitirem a rede VIP pública para aceder a recursos de infraestrutura. no 1811, esta alteração será aplicada a todos os sistemas. Se aplicar a correção no 1809, não é necessária nenhuma ação adicional.  

### <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as seguintes atualizações de segurança:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Para obter mais informações sobre essas vulnerabilidades, clique nos links anteriores ou veja os artigos da Base de dados de conhecimento da Microsoft [4457131](https://support.microsoft.com/help/4457131) e [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Pré-requisitos

- Instale a correção de pilha mais recente do Azure para 1808 antes de aplicar 1809. Para obter mais informações, consulte [KB 4481066 – Azure Stack correção do Azure Stack correção 1.1808.9.117](https://support.microsoft.com/help/4481066/). Embora a Microsoft recomenda a correção mais recente disponível, a versão mínima necessária para instalar 1809 é 1.1808.5.110.

  > [!TIP]  
  > Subscrever o seguinte procedimento *RRS* ou *Atom* feeds para se manter atualizado com correções do Azure Stack:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Antes de iniciar a instalação desta atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os parâmetros seguintes para validar o status do seu Azure Stack e resolver quaisquer problemas operacionais detectados, incluindo todos os avisos e falhas. Também rever alertas ativos e resolver qualquer um que requerem uma ação.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```

- Quando o Azure Stack é gerido pelo System Center Operations Manager (SCOM), certifique-se de que atualizar o pacote de gestão para o Microsoft Azure Stack para a versão 10.0.3.11 antes de aplicar 1809.

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando executa [AzureStack teste](azure-stack-diagnostic-test.md) após a atualização 1809, será apresentada uma mensagem de aviso do controlador de gestão de placas base (BMC). Pode ignorar este aviso.

- <!-- 2468613 - IS --> Durante a instalação desta atualização, poderá ver alertas com o título *erro – o modelo para typu FaultType UserAccounts.New está em falta.*  Pode ignorar com segurança estes alertas. Estes alertas serão fechado automaticamente depois de concluída a instalação desta atualização.

- <!-- 2489559 - IS --> Não tente criar máquinas virtuais durante a instalação desta atualização. Para obter mais informações sobre a gestão de atualizações, consulte [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Se aplicou uma atualização para o Azure Stack de seu OEM, a **atualização disponível** notificação poderá não aparecer no portal de administração do Azure Stack. Para instalar a atualização da Microsoft, transfira e importe-o manualmente utilizando as instruções localizadas aqui [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Passos de pós-atualização

> [!Important]  
> Prepare-se a implementação do Azure Stack para o anfitrião de extensão que está ativada pelo pacote de atualização seguinte. Preparar o seu sistema usando as seguintes orientações [preparar para o anfitrião de extensão para o Azure Stack](azure-stack-extension-host-prepare.md).

Após a instalação desta atualização, instale as correções aplicáveis. Para obter mais informações, consulte os seguintes artigos da base de dados de conhecimento, bem como nossos [política de manutenção](azure-stack-servicing-policy.md).  
- [KB 4481548 – o Azure Stack correção Azure Stack correção 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Seguem-se após a instalação problemas conhecidos para esta versão de compilação.

### <a name="portal"></a>Portal

- A documentação técnica do Azure Stack enfoca a versão mais recente. Devido a alterações de portais entre versões, o que vê quando utilizar os portais do Azure Stack pode variar do que vê na documentação.

<!-- 2930718 - IS ASDK --> 
- No portal do administrador, ao acessar os detalhes de qualquer subscrição do utilizador, depois de fechar o painel e clique em **recente**, não é apresentado o nome de subscrição de utilizador.

<!-- 3060156 - IS ASDK --> 
- O administrador e o utilizador portais, clicando em definições do portal e selecionando **eliminar todas as definições e dashboards privados** não funcionar conforme esperado. É apresentada uma notificação de erro. 

<!-- 2930799 - IS ASDK --> 
- O administrador e o utilizador portais, em **todos os serviços**, o elemento **planos de proteção DDoS** incorretamente está listado. Não está disponível no Azure Stack. Se tentar criá-lo, é apresentado um erro a indicar que o portal não foi possível criar o item do marketplace. 

<!-- 2930820 - IS ASDK --> 
- Nos portais do administrador e o utilizador, se pesquisar por "Docker", o item incorretamente é devolvido. Não está disponível no Azure Stack. Se tentar criá-lo, é apresentado um painel com uma indicação de erro. 

<!-- 2967387 – IS, ASDK --> 
- A conta que utiliza para iniciar sessão no portal de utilizador ou administrador do Azure Stack indicará **utilizador não identificado**. Esta mensagem é apresentada quando a conta não tem qualquer um *primeira* ou *última* nome especificado. Para contornar este problema, edite a conta de utilizador para fornecer o primeiro ou último nome. Deve, em seguida, termine sessão e, em seguida, inicie sessão novamente para o portal.  

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
   
  Executar o [AzureStack teste](azure-stack-diagnostic-test.md) cmdlet para verificar o estado de funcionamento das instâncias de função de infraestrutura e dimensionar nós de unidade. Se não existem problemas são detetados pelo [AzureStack teste](azure-stack-diagnostic-test.md), pode ignorar estes alertas. Se for detetado um problema, pode tentar iniciar a instância de função de infraestrutura ou o nó com o portal de administração ou PowerShell.

  Este problema é resolvido da versão mais recente [versão de correção de 1809](https://support.microsoft.com/help/4481548/), por isso, certifique-se de que instale esta correção, se estiver a ter o problema. 

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
- Poderá ver um alerta para o **armazenamento** componente que tem os seguintes detalhes:

   - NOME: Erro de comunicação interno do serviço de armazenamento  
   - GRAVIDADE: Crítica  
   - COMPONENTE: Armazenamento  
   - DESCRIÇÃO: Ocorreu um erro de comunicação interna do serviço de armazenamento quando enviar pedidos para os seguintes nós.  

    O alerta pode ser ignorado com segurança, mas terá de fechar o alerta manualmente.

<!-- 2368581 - IS, ASDK --> 
- Um operador do Azure Stack, se receber um alerta de pouca memória e máquinas de virtuais de inquilino não for possível implementar com um **erro de criação de recursos de infraestrutura de VM**, é possível que o carimbo de data / Azure Stack está disponível memória esgotada. Utilize o [Planeador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para melhor compreender a capacidade disponível para as cargas de trabalho.

### <a name="compute"></a>Computação

- Ao criar um [VM da série Dv2](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), D11 14v2 VMs permitem-lhe criar 4, 8, 16 e discos de dados de 32, respetivamente. No entanto, o painel VM de criar mostra 8, 16, 32 e 64 discos de dados.

<!-- 3235634 – IS, ASDK -->
- Para implementar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especifique o sufixo como **Standard_A2_v2** (v em minúsculas). Não utilize **Standard_A2_V2** (V maiúsculo). Isso funciona no global Azure e é uma inconsistência no Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Quando cria uma nova máquina de virtual (VM) com o portal do Azure Stack, e selecionar o tamanho da VM, a coluna USD/mês é apresentada com um **indisponível** mensagem. Esta coluna não deve aparecer; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Ao utilizar o [ **Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), tem de utilizar o **- OsUri** parâmetro como a conta de armazenamento URI onde o disco é carregado. Se utilizar o caminho local do disco, o cmdlet falhar com o seguinte erro: *Operação de longa execução falhou com o estado 'Com falhas'*. 

<!--  2795678 – IS, ASDK --> 
- Ao utilizar o portal para criar máquinas virtuais (VM) num tamanho VM premium (DS, Ds_v2, FS, FSv2), a VM é criada numa conta de armazenamento standard. Criação de uma conta de armazenamento standard não afeta funcionalmente, IOPs, ou de faturação. 

   Pode ignorar o aviso que diz: *Que escolheu utilizar um disco padrão num tamanho que suporte discos premium. Isso pode afetar o desempenho do sistema operativo e não é recomendado. Considere utilizar o armazenamento premium (SSD) em vez disso.*

<!-- 2967447 - IS, ASDK --> 
- A experiência de criação de conjunto (VMSS) de dimensionamento de máquina virtual fornece 7.2 baseada em CentOS, como uma opção para implementação. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para a sua implementação ou utilizar um modelo Azure Resource Manager, especificando outra imagem do CentOS que tenha sido transferida antes da implantação do marketplace pela operadora de rede.  

<!-- 2724873 - IS --> 
- Ao utilizar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** para gerir unidades de escala, a primeira tentativa para iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução deve ser bem-sucedida para concluir a operação. 

<!-- TBD - IS ASDK --> 
- Se uma extensão numa implantação de VM o aprovisionamento demora demasiado tempo, os utilizadores devem permitir que o limite de tempo de aprovisionamento em vez de tentar interromper o processo para desaloque ou elimine a VM.  

<!-- 1662991 IS ASDK --> 
- Diagnóstico de VM do Linux não é suportado no Azure Stack. Quando implementa uma VM do Linux com o diagnóstico VM ativado, a implementação falhar. A implementação falhar, também, se ativar as métricas básicas de VM do Linux através das definições de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Quando registra o **Microsoft.Insight** fornecedor de recursos nas definições de subscrição e criar uma VM do Windows com o convidado SO diagnóstico ativado, o gráfico de percentagem de CPU na página de descrição geral de VM não mostra os dados das métricas.

   Para localizar dados de métricas, como o gráfico de percentagem de CPU para a VM, vá para a janela de métricas e mostrar todas as as VM de Windows convidado métricas suportadas.

<!-- 3507629 - IS, ASDK --> 
- Discos geridos cria dois novos [tipos de quota de computação](azure-stack-quota-types.md#compute-quota-types) para limitar a capacidade máxima de discos geridos que podem ser aprovisionados. Por predefinição, 2048 GiB é alocado para cada tipo de quota de discos geridos. No entanto, pode encontrar os seguintes problemas:

   - Para quotas criadas antes da atualização de 1808, a quota de Managed Disks mostrará 0 valor no portal do administrador, embora 2048 GiB é alocado. Pode aumentar ou diminuir o valor com base nas suas necessidades reais e recentemente defina o valor da quota substitui a predefinição do 2048 GiB.
   - Se atualizar o valor da quota como 0, é equivalente para o valor predefinido de 2048 GiB. Como solução, defina o valor de quota para 1.

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1809 de atualização, poderá encontrar os seguintes problemas ao implementar VMs com discos geridos:

   - Se a subscrição tiver sido criada antes da atualização de 1808, implementar uma VM com Managed Disks poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos para cada subscrição:
      1. No portal do inquilino, aceda a **subscrições** e encontrar a subscrição. Clique em **fornecedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **voltar a registar**.
      2. Na mesma subscrição, aceda a **controlo de acesso (IAM)** e certifique-se de que o **AzureStack-DiskRP-Client** função está listada.
   2. Se tiver configurado o ambiente multi-inquilino, a implementação de VMs numa assinatura associada um diretório de convidado poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos [este artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) reconfigurar cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização de SSH ativada não permitirá que use as chaves SSH para iniciar sessão. Como solução, utilize o acesso VM para a extensão do Linux para implementar as chaves SSH após o aprovisionamento ou utilizar autenticação baseada em palavra-passe.

### <a name="networking"></a>Redes  

<!-- 1766332 - IS ASDK --> 
- Sob **Networking**, se clicar em **criar Gateway de VPN** para configurar uma ligação de VPN, **baseado em política** está listado como um tipo de VPN. Não selecione esta opção. Apenas os **rota com base** opção é suportada no Azure Stack.

<!-- 1902460 - IS ASDK --> 
- O Azure Stack oferece suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as subscrições de inquilino. Após a criação da primeira rede local ligação de gateway, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

<!-- 16309153 - IS ASDK --> 
- Numa rede Virtual que foi criado com uma definição de servidor DNS de *automática*, a mudança para uma falha de servidor DNS personalizado. As definições atualizadas não são enviadas para as VMs nessa vnet.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *segredo rotação*, existe um período em que os endereços IP públicos estão inacessíveis para dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
-   Em cenários em que o inquilino está a aceder a suas máquinas virtuais utilizando um túnel S2S VPN, poderão surgir um cenário em que as tentativas de ligação falharem se a sub-rede no local foi adicionada ao Gateway de rede Local já foi criado o gateway. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>Serviço de Aplicações

<!-- 2352906 - IS ASDK --> 
- Os utilizadores tem de registar o fornecedor de recursos de armazenamento antes de ser criar sua primeira função do Azure na subscrição.


### <a name="usage"></a>Utilização  

<!-- TBD - IS ASDK --> 
- Os públicos dados de medidor de utilização do endereço IP mostram o mesmo *EventDateTime* valor para cada registo, em vez do *TimeDate* carimbo que mostra quando o registo foi criado. Atualmente, não pode utilizar estes dados para efetuar gestão de contas preciso de utilização de endereços IP pública.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização do Azure Stack 1809 partir [aqui](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Passos Seguintes

- Para rever a política de manutenção para sistemas integrados do Azure Stack e o que deve fazer para manter seu sistema num estado suportado, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para utilizar o ponto final com privilégios (PEP) para monitorizar e retomar as atualizações, consulte [monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios](azure-stack-monitor-update.md).  
- Para uma descrição geral da gestão de atualizações no Azure Stack, veja [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar as atualizações com o Azure Stack, veja [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).  
