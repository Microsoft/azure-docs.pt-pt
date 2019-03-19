---
title: Atualização de 1811 de pilha do Azure | Documentos da Microsoft
description: Saiba mais sobre a atualização de 1811 para o sistema integrado do Azure Stack, incluindo o que há de novo, onde pode transferir a atualização e problemas conhecidos.
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
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: ddcf3428f32698c9825f13975929bc4677139acf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58081054"
---
# <a name="azure-stack-1811-update"></a>Atualização de 1811 de pilha do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1811. O pacote de atualização inclui melhorias, correções e novas funcionalidades para esta versão do Azure Stack. Este artigo também descreve os problemas conhecidos nesta versão e inclui uma ligação para que possa transferir a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados com o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é apenas para sistemas integrados do Azure Stack. Não é aplicável este pacote de atualização para o Azure Stack Development Kit.

## <a name="build-reference"></a>Criar a referência

É o número de compilação de atualização do Azure Stack 1811 **1.1811.0.101**.

## <a name="hotfixes"></a>Correções

O Azure Stack lança correções em intervalos regulares. Certifique-se de que instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1809 antes de atualizar o Azure Stack para 1811.

> [!TIP]  
> Subscrever o seguinte procedimento *RSS* ou *Atom* feeds para se manter atualizado com correções do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Correções de pilha do Azure

- **1809**: [KB 4481548 – o Azure Stack correção 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Sem correções atual disponível.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Durante a instalação da atualização 1811, tem de se certificar de que todas as instâncias do portal do administrador estão fechadas. O portal de utilizador pode permanecer aberto, mas tem de fechar o portal de administração.

- Prepare-se a implementação do Azure Stack para o anfitrião de extensão do Azure Stack. Prepare o seu sistema usando as seguintes orientações: [Preparar para o anfitrião de extensão para o Azure Stack](azure-stack-extension-host-prepare.md). 
 
- Instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1809 antes de atualizar para 1811.

- Antes de iniciar a instalação desta atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os parâmetros seguintes para validar o status do seu Azure Stack e resolver quaisquer problemas operacionais detectados, incluindo todos os avisos e falhas. Também rever alertas ativos e resolver qualquer um que requerem uma ação.  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Se não tiver os requisitos de anfitrião de extensão cumpridos, o `Test-AzureStack` saída apresenta a seguinte mensagem: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- A atualização do Azure Stack 1811 requer que os certificados de anfitrião de extensão obrigatório corretamente tiver importado para o seu ambiente do Azure Stack. Para continuar com a instalação da atualização 1811, tem de importar os certificados SSL necessários para o anfitrião de extensão. Para importar os certificados, consulte [esta secção](azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Se ignorar cada aviso e ainda optar por instalar a atualização de 1811, a atualização falhará em aproximadamente 1 hora, com a seguinte mensagem:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Depois de importar os certificados de anfitrião de extensão obrigatório corretamente, pode retomar a atualização de 1811 a partir do portal do administrador. Embora a Microsoft solicitará operadores do Azure Stack para agendar uma janela de manutenção durante o processo de atualização, uma falha devido aos certificados de anfitrião de extensão em falta não deve afetar as cargas de trabalho ou serviços existentes.  

    Durante a instalação desta atualização, o portal de utilizador do Azure Stack está disponível enquanto o anfitrião de extensão está a ser configurado. A configuração do anfitrião de extensão pode demorar até 5 horas. Durante esse período, poderá verificar o estado de uma atualização ou retomar uma instalação de atualização falhada utilizando o [PowerShell de Administrador do Azure Stack ou o ponto final com privilégios](azure-stack-monitor-update.md).

- Quando o Azure Stack é gerido pelo System Center Operations Manager (SCOM), certifique-se de que atualizar o pacote de gestão para o Microsoft Azure Stack para a versão 1.0.3.11 antes de aplicar 1811.

## <a name="new-features"></a>Novos recursos

Esta atualização inclui as seguintes novas funcionalidades e melhorias para o Azure Stack:

- Com esta versão, o [anfitrião de extensão](azure-stack-extension-host-prepare.md) está ativada. O anfitrião de extensão simplifica a integração da rede e melhora a postura de segurança do Azure Stack.

- Foi adicionado suporte para autenticação do dispositivo com o Active Directory Federated Services (AD FS), ao utilizar a CLI do Azure em particular. Para obter mais informações, consulte [perfis de versão de API de utilização com a CLI do Azure no Azure Stack](./user/azure-stack-version-profiles-azurecli2.md)

- Foi adicionado suporte para principais de serviço com um segredo do cliente com o Active Directory Federated Services (AD FS). Para obter mais informações, consulte [criar principal de serviço para o AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Esta versão adiciona suporte para as seguintes versões de API de serviço de armazenamento do Azure: **2017-07-29**, **2017-11-09**. Também é adicionado suporte para as seguintes versões de API de fornecedor de recursos de armazenamento do Azure: **2016-05-01**, **2016-12-01**, **2017-06-01**, e **2017-10-01**. Para obter mais informações, consulte [armazenamento do Azure Stack: Diferenças e considerações](./user/azure-stack-acs-differences.md).

- Foram adicionados novos a comandos com privilégios de ponto de extremidade para atualizar e remover os princípios de serviço para ADFS. Para obter mais informações, consulte [criar principal de serviço para o AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Foi adicionadas novas operações de nó de unidade de escala que permitem que um operador do Azure Stack iniciar, parar e encerrar um nó de unidade de escala. Para obter mais informações, consulte [ações do nó de unidade de dimensionamento no Azure Stack](azure-stack-node-actions.md).

- Adicionado um novo painel de propriedades da região que apresenta detalhes de registo do ambiente. Pode ver estas informações ao clicar o **gestão da região** mosaico no dashboard do padrão no portal do administrador e, em seguida, selecionando **propriedades**.

- Adicionado um novo comando de ponto final com privilégios para atualizar a credencial BMC com o nome de utilizador e palavra-passe, utilizado para comunicar com as máquinas físicas. Para obter mais informações, consulte [atualizar o controlador \(BMC) credencial](azure-stack-rotate-secrets.md).

- Adicionada a capacidade de acessar o mapa do Azure que o ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do administrador e utilizador os portais, semelhantes à forma como ele está disponível no portal do Azure.

- Adicionar uma gestão de Marketplace melhorada experiência para utilizadores desligados. O processo de carregamento para publicar um item do mercado num ambiente desligado é simplificado para uma etapa, em vez de carregar a imagem e o pacote do Marketplace em separado. O produto carregado também será visível no painel de gestão do Marketplace. Para obter mais informações, consulte [esta secção](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher). 

- Esta versão reduz a janela de manutenção necessárias para a rotação de secreta ao adicionar a capacidade de rodar apenas os certificados externos durante [rotação secreta do Azure Stack](azure-stack-rotate-secrets.md).

- [O Azure Stack do PowerShell](azure-stack-powershell-install.md) foi atualizada para a versão 1.6.0. A atualização inclui suporte para os novos recursos relacionados com o armazenamento no Azure Stack. Para obter mais informações, consulte as notas de versão para o [módulo de administração do Azure Stack 1.6.0 na galeria do PowerShell](https://www.powershellgallery.com/packages/AzureStack/1.6.0) para obter informações sobre a atualizar ou instalar o Azure Stack do PowerShell, consulte [instalar o PowerShell para O Azure Stack](azure-stack-powershell-install.md).

- Discos geridos está agora ativado por predefinição, ao criar máquinas virtuais com o portal do Azure Stack. Consulte a [problemas conhecidos](#known-issues-post-installation) secção para obter os passos adicionais necessários para os Managed Disks evitar falhas na criação de VM.

- Essa versão apresenta o alerta **reparação** ações para o operador do Azure Stack. Alguns alertas no 1811 fornecem uma **reparação** botão no alerta que pode selecionar para resolver o problema. Para obter mais informações, consulte [monitorizar o estado de funcionamento e alertas no Azure Stack](azure-stack-monitor-health.md).

- Atualizações para a experiência de atualização no Azure Stack. As atualização as melhorias incluem: 
  - Guias que dividir as atualizações do histórico de atualização para um melhor controlo atualizações em curso e concluídas atualizações.
  - Visualizações de estado melhorada na secção essentials com novos ícones e layout para as versões atual e do OEM, bem como a última atualizadas data.
  - **Vista** ligar para a coluna de notas de versão leva o usuário diretamente para a documentação específica para essa atualização em vez da página atualizar genérico.
  - O **atualizar o histórico de** separador utilizado para determinar os tempos de execução para cada uma das atualizações, bem como capacidades de filtragem melhoradas.  
  - Unidades de escala de pilha do Azure que estão ligadas ainda automaticamente receberá **atualização disponível** à medida que ficam disponíveis.
  - Unidades de escala de pilha do Azure que estão ligadas não podem importar as atualizações de tal como antes. 
  - Não há nenhuma alteração no processo para transferir os registos JSON a partir do portal. Operadores do Azure Stack irão ver passos expressar progresso a expandir.

    Para obter mais informações, consulte [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Problemas corrigidos

<!-- TBD - IS ASDK --> 
- Foi corrigido um problema em que o públicos dados de medidor de utilização do endereço IP mostraram o mesmo **EventDateTime** valor para cada registo, em vez do **TimeDate** carimbo que mostra quando o registo foi criado. Agora, pode utilizar estes dados efetuar gestão de contas preciso de utilização de endereços IP pública.

<!-- 3099544 – IS, ASDK --> 
- Foi corrigido um problema que ocorreram durante a criação de uma nova máquina de virtual (VM) com o portal do Azure Stack. Selecionar o tamanho da VM devido a **USD/mês** coluna para apresentar uma **indisponível** mensagem. Já não aparece nesta coluna; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Foi corrigido um problema no qual o portal de administrador, ao acessar os detalhes de qualquer subscrição do utilizador, depois de fechar o painel e clique em **recente**, o nome de subscrição de utilizador não sejam apresentados. O nome de subscrição de utilizador aparece agora.

<!-- 3060156 - IS ASDK --> 
- Foi corrigido um problema nos portais do administrador e o utilizador: clicar em definições do portal e selecionando **eliminar todas as definições e dashboards privados** não funcionou como esperado e foi apresentada uma notificação de erro. Esta opção agora funciona corretamente.

<!-- 2930799 - IS ASDK --> 
- Foi corrigido um problema nos portais do administrador e o utilizador: sob **todos os serviços**, o elemento **planos de proteção DDoS** incorretamente estava listado. Não está disponível no Azure Stack. A listagem foi removida.
 
<!--2760466 – IS  ASDK --> 
- Foi corrigido um problema que ocorreu ao instalar um novo ambiente do Azure Stack, no qual o alerta que indica **ativação necessária** não a apresentar. Agora exiba corretamente.

<!--1236441 – IS  ASDK --> 
- Foi corrigido um problema que impediu a aplicação de políticas RBAC para um grupo de utilizadores ao utilizar o ADFS.

<!--3463840 - IS, ASDK --> 
- Foi corrigido um problema com a falhar devido a um servidor de ficheiros inacessíveis da rede VIP pública cópias de segurança da infraestrutura. Esta correção Retroceder o serviço de cópia de segurança da infraestrutura para a rede de infraestrutura públicas. Se se inscreveu a versão mais recente [correção do Azure Stack para 1809](#azure-stack-hotfixes) que resolve esse problema, a atualização de 1811 não fará com que quaisquer modificações adicionais. 

<!-- 2967387 – IS, ASDK --> 
- Foi corrigido um problema em que a conta que utilizou para iniciar sessão no portal de utilizador ou administrador do Azure Stack são apresentadas como **utilizador não identificado**. Esta mensagem foi apresentada quando a conta não tem qualquer um **primeira** ou **última** nome especificado.   

<!--  2873083 - IS ASDK --> 
- Foi corrigido um problema no qual com o portal para criar um conjunto de dimensionamento de máquinas virtuais (VMSS) causado o **tamanho da instância** menu pendente para não carregar corretamente ao utilizar o Internet Explorer. Este browser agora funciona corretamente.  

<!-- 3190553 - IS ASDK -->
- Foi corrigido um problema que gerou alertas desnecessárias que indica que uma instância de função de infraestrutura não estava disponível ou nó de unidade de escala estava offline.

<!-- 2724961 - IS ASDK -->
- Fiexed um problema em que a página de descrição geral VM não é possível mostrar corretamente o gráfico de métricas da VM. 

## <a name="changes"></a>Alterações

- Uma nova forma de ver e editar as quotas num plano é introduzida no 1811. Para obter mais informações, consulte [ver uma quota existente](azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Aprimoramentos de segurança nesta atualização resultam num aumento no tamanho da função de serviço de diretório de cópia de segurança. Para atualizada a documentação de orientação para a localização de armazenamento externo de dimensionamento, veja a [documentação de cópia de segurança de infra-estrutura](azure-stack-backup-reference.md#storage-location-sizing). Esta alteração resulta em mais tempo para concluir a cópia de segurança devido a transferência de dados de tamanho maior. Esta alteração irá afetar os sistemas integrados. 

- O cmdlet PEP existente para obter as chaves de recuperação do BitLocker é mudado na 1811, de Get-AzsCsvsRecoveryKeys para Get-AzsRecoveryKeys. Para obter mais informações sobre como obter as chaves de recuperação do BitLocker, consulte [instruções sobre como obter as chaves](azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as seguintes atualizações de segurança:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Para obter mais informações sobre essas vulnerabilidades, clique nos links anteriores ou veja os artigos da Base de dados de conhecimento da Microsoft [4478877](https://support.microsoft.com/help/4478877).

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando executa o **Get-AzureStackLog** cmdlet do PowerShell após a execução **teste AzureStack** na mesma sessão de ponto final com privilégios (PEP), **Get-AzureStackLog** falhar. Para contornar este problema, feche a sessão PEP em que é executado **teste AzureStack**e, em seguida, abra uma nova sessão para ser executado **Get-AzureStackLog**.

- Durante a instalação do 1811, certifique-se de que todas as instâncias do portal do administrador estão fechadas durante este período. O portal de utilizador pode permanecer aberto, mas tem de fechar o portal de administração.

- Quando em execução [teste AzureStack](azure-stack-diagnostic-test.md), se qualquer um da **AzsInfraRoleSummary** ou o **AzsPortalApiSummary** teste falhar, se lhe for pedido para executar  **Teste AzureStack** com o `-Repair` sinalizador.  Se executar este comando, ocorre uma falha com a seguinte mensagem de erro:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  Este problema será corrigido numa versão futura.

- Durante a instalação da atualização 1811, o portal de utilização do Azure Stack está disponível enquanto o anfitrião de extensão está a ser configurado. A configuração do anfitrião de extensão pode demorar até 5 horas. Durante esse período, poderá verificar o estado de uma atualização ou retomar uma instalação de atualização falhada utilizando o [PowerShell de Administrador do Azure Stack ou o ponto final com privilégios](azure-stack-monitor-update.md). 

- Durante a instalação da atualização 1811, dashboard do portal de utilizador pode não estar disponível e personalizações podem ser perdidas. Pode restaurar o dashboard para a predefinição após a conclusão da atualização abrindo as definições do portal e selecionando **restaurar predefinições**.

- Quando executa [AzureStack teste](azure-stack-diagnostic-test.md), será apresentada uma mensagem de aviso do controlador de gestão de placas base (BMC). Pode ignorar este aviso.

- <!-- 2468613 - IS --> Durante a instalação desta atualização, poderá ver alertas com o título `Error – Template for FaultType UserAccounts.New is missing.` pode ignorar com segurança estes alertas. Os alertas fechar automaticamente depois de concluída a instalação desta atualização.

- <!-- 3139614 | IS --> Se aplicou uma atualização para o Azure Stack de seu OEM, a **atualização disponível** notificação poderá não aparecer no portal de administrador do Azure Stack. Para instalar a atualização da Microsoft, transfira e importe-o manualmente utilizando as instruções localizadas aqui [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Passos de pós-atualização

- Após a instalação desta atualização, instale as correções aplicáveis. Para obter mais informações, consulte [correções](#hotfixes), bem como nossos [política de manutenção](azure-stack-servicing-policy.md).  

- Obter os dados com as chaves de encriptação do rest e com segurança armazená-las fora da sua implementação do Azure Stack. Siga os [instruções sobre como obter as chaves](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Seguem-se após a instalação problemas conhecidos para esta versão de compilação.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- Nos portais do administrador e o utilizador, se pesquisar por "Docker", o item incorretamente é devolvido. Não está disponível no Azure Stack. Se tentar criá-lo, é apresentado um painel com uma indicação de erro. 

<!-- 2931230 – IS  ASDK --> 
- Não não possível eliminar os planos que são adicionados a uma subscrição de utilizador como um plano de suplemento, mesmo quando remover o plano de subscrição de utilizador. O plano irá permanecer até que as subscrições que referenciam o plano de suplemento também são eliminadas. 

<!-- TBD - IS ASDK --> 
- Os dois tipos de subscrição administrativas que foram introduzidos com versão 1804 não devem ser utilizados. Os tipos de subscrição são **medição subscrição**, e **subscrição de consumo**. Esses tipos de subscrição são visíveis no novos ambientes do Azure Stack a partir da versão 1804, mas ainda não estão prontos a utilizar. Deve continuar a utilizar o **fornecedor predefinido** tipo de subscrição.

<!-- TBD - IS ASDK --> 
- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos inteiro e, em seguida, elimine as subscrições de utilizador.

<!-- TBD - IS ASDK --> 
- Não é possível ver as permissões à sua subscrição a utilizar os portais do Azure Stack. Como solução, utilize [PowerShell para verificar as permissões](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Estado de funcionamento e monitorização

<!-- 1264761 - IS ASDK -->  
- Poderá ver alertas para o **controlador de estado de funcionamento** componente que tem os seguintes detalhes:  

  - #1 do alerta:
     - NOME:  Função de infraestrutura mau estado de funcionamento
     - GRAVIDADE: Aviso
     - COMPONENTE: Controlador de estado de funcionamento
     - DESCRIÇÃO: O Scanner de Heartbeat do controlador de estado de funcionamento não está disponível. Isto pode afetar as métricas e relatórios de estado de funcionamento.  

  - Alerta #2:
     - NOME:  Função de infraestrutura mau estado de funcionamento
     - GRAVIDADE: Aviso
     - COMPONENTE: Controlador de estado de funcionamento
     - DESCRIÇÃO: O Scanner de falhas de controlador do Estado de funcionamento não está disponível. Isto pode afetar as métricas e relatórios de estado de funcionamento.

    Ambos os alertas podem ser ignoradas com segurança. Será fechada automaticamente ao longo do tempo.  

### <a name="compute"></a>Computação

- Ao criar um novo Windows Máquina Virtual (VM), o **definições** painel requer que seleciona uma porta de entrada pública para continuar. 1811, esta definição é necessária, mas não tem qualquer efeito. Isto acontece porque a funcionalidade depende da Firewall do Azure, que não está implementado no Azure Stack. Pode selecionar **portas de entrada públicas não**, ou qualquer uma das outras opções para prosseguir com a criação de VM. A definição não tem qualquer efeito.

- Ao criar um novo Windows Máquina Virtual (VM), poderá ser apresentado o erro seguinte:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   O erro ocorre se ativar diagnósticos de arranque numa VM, mas eliminar a sua conta de armazenamento de diagnósticos de arranque. Para contornar este problema, recrie a conta de armazenamento com o mesmo nome que utilizou anteriormente.

- Ao criar um [VM da série Dv2](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), D11 14v2 VMs permitem-lhe criar 4, 8, 16 e discos de dados de 32, respetivamente. No entanto, o painel VM de criar mostra 8, 16, 32 e 64 discos de dados.

- Registos de utilização no Azure Stack podem conter capitalização inesperada; Por exemplo:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   Neste exemplo, o nome do grupo de recursos deve ser **AndrewRG**. Pode ignorar com segurança essa inconsistência.

<!-- 3235634 – IS, ASDK -->
- Para implementar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especifique o sufixo como **Standard_A2_v2** (v em minúsculas). Não utilize **Standard_A2_V2** (V maiúsculo). Isso funciona no global Azure e é uma inconsistência no Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Ao utilizar o [ **Add-AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage), tem de utilizar o **- OsUri** parâmetro como a conta de armazenamento URI onde o disco é carregado. Se utilizar o caminho local do disco, o cmdlet falhar com o seguinte erro: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Ao utilizar o portal para criar máquinas virtuais (VMs) num tamanho VM premium (DS, Ds_v2, FS, FSv2), a VM é criada numa conta de armazenamento standard. Criação de uma conta de armazenamento standard não afeta funcionalmente, IOPs, ou de faturação. Pode ignorar o aviso que diz: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- A experiência de criação de conjunto (VMSS) de dimensionamento de máquina virtual fornece 7.2 baseada em CentOS, como uma opção para implementação. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para a sua implementação, ou utilizar um modelo do Azure Resource Manager, especificando outra imagem do CentOS que tenha sido transferida antes da implementação a partir do mercado, o operador.  

<!-- 2724873 - IS --> 
- Ao utilizar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** para gerir unidades de escala, a primeira tentativa para iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução deve ser concluído com êxito a operação. 

<!-- TBD - IS ASDK --> 
- Se uma extensão numa implantação de VM o aprovisionamento demora demasiado tempo, permitir que o limite de tempo de aprovisionamento em vez de tentar interromper o processo para desaloque ou elimine a VM.  

<!-- 1662991 IS ASDK --> 
- Diagnóstico de VM do Linux não é suportado no Azure Stack. Quando implementa uma VM do Linux com o diagnóstico VM ativado, a implementação falhar. A implementação falhar, também, se ativar as métricas básicas de VM do Linux através das definições de diagnóstico.  

<!-- 3507629 - IS, ASDK --> 
- Discos geridos cria dois novos [tipos de quota de computação](azure-stack-quota-types.md#compute-quota-types) para limitar a capacidade máxima de discos geridos que podem ser aprovisionados. Por predefinição, 2048 GiB é alocado para cada tipo de quota de discos geridos. No entanto, pode encontrar os seguintes problemas:

   - Para quotas criadas antes da atualização de 1808, a quota de Managed Disks mostrará 0 valor no portal do administrador, embora 2048 GiB é alocado. Pode aumentar ou diminuir o valor com base nas suas necessidades reais e recentemente defina o valor da quota substitui a predefinição do 2048 GiB.
   - Se atualizar o valor da quota como 0, é equivalente para o valor predefinido de 2048 GiB. Como solução, defina o valor de quota para 1.

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1811 de atualização, poderá encontrar os seguintes problemas ao implementar VMs com discos geridos:

   - Se a subscrição tiver sido criada antes da atualização de 1808, implementar uma VM com Managed Disks poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos para cada subscrição:
      1. No portal do inquilino, aceda a **subscrições** e encontrar a subscrição. Selecione **fornecedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **voltar a registar**.
      2. Na mesma subscrição, aceda a **controlo de acesso (IAM)** e certifique-se de que o **AzureStack-DiskRP-Client** função está listada.
   - Se tiver configurado o ambiente multi-inquilino, a implementação de VMs numa assinatura associada um diretório de convidado poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos [este artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) reconfigurar cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização de SSH ativada não permitirá que use as chaves SSH para iniciar sessão. Como solução, utilize o acesso VM para a extensão do Linux para implementar as chaves SSH após o aprovisionamento ou utilizar a autenticação baseada em palavra-passe.

### <a name="networking"></a>Redes  

<!-- 1766332 - IS ASDK --> 
- Sob **Networking**, se clicar em **criar Gateway de VPN** para configurar uma ligação de VPN, **baseado em política** está listado como um tipo de VPN. Não selecione esta opção. Apenas os **rota com base** opção é suportada no Azure Stack.

<!-- 1902460 - IS ASDK --> 
- O Azure Stack oferece suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as subscrições de inquilino. Após a criação da primeira rede local ligação de gateway, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são rejeitados.

<!-- 16309153 - IS ASDK --> 
- Numa rede virtual que foi criada com uma definição de servidor DNS de **automática**, mudando para um servidor DNS personalizado falha. As definições atualizadas não são enviadas para as VMs nessa vnet.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *segredo rotação*, existe um período em que os endereços IP públicos estão inacessíveis para dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
- Em cenários em que o inquilino está a aceder a máquinas virtuais utilizando um túnel S2S VPN, poderão surgir um cenário em que as tentativas de ligação falharem se a sub-rede no local foi adicionada ao gateway de rede local já foi criado o gateway. 

- No portal do Azure Stack, quando altera um endereço IP estático para uma configuração de IP que está vinculado a um adaptador de rede anexado a uma instância VM, verá uma mensagem de aviso que indica 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Pode ignorar esta mensagem; o endereço IP será alterado, mesmo que a instância VM não é reinicializado.

- No portal, no **propriedades de rede** painel existe uma ligação para **regras de segurança efetivas** para cada adaptador de rede. Se selecionar esta ligação, abrirá um novo painel que mostra a mensagem de erro `Not Found.` este erro ocorre porque o Azure Stack ainda não suporta **regras de segurança efetivas**.

- No portal, se adicionar uma regra de segurança de entrada e selecione **etiquetas de serviço** como a origem, várias opções são apresentadas no **marca de origem** lista que não estão disponíveis para o Azure Stack. As únicas opções que são válidas no Azure Stack são os seguintes:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    As outras opções não são suportadas como etiquetas de origem no Azure Stack. Da mesma forma, se adicionar uma regra de segurança de saída e selecione **etiquetas de serviço** como o destino, a mesma lista de opções para **marca de origem** é apresentado. As opções apenas válidas são os mesmos que para **marca de origem**, conforme descrito na lista anterior.

- O **New-AzureRmIpSecPolicy** cmdlet do PowerShell não suporta a definição **DHGroup24** para o `DHGroup` parâmetro.

- Grupos de segurança de rede (NSGs) não funcionam no Azure Stack estar na mesma forma como global. No Azure, pode definir várias portas numa regra NSG (com o portal, PowerShell e modelos do Resource Manager). No Azure Stack, não é possível definir várias portas numa regra NSG através do portal. Para contornar este problema, utilize um modelo do Resource Manager para definir estas regras adicionais.

### <a name="infrastructure-backup"></a>Cópia de segurança da infraestrutura

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Depois de ativar as cópias de segurança automáticas, o serviço de agendador entra em estado desativado inesperadamente. O serviço do controlador de cópia de segurança irá detetar que cópias de segurança automáticas estão desativadas e criar um aviso no portal do administrador. Este aviso é expectável quando se cópias de segurança automáticas estão desativadas. 
    - Causa: Este problema é devido a um erro no serviço que resulte em perda de configuração do programador. Esse bug não altera a localização de armazenamento, nome de utilizador, palavra-passe ou chave de encriptação.   
    - Remediação: Para atenuar este problema, abra o painel de definições de cópia de segurança do controlador no fornecedor de recursos de cópia de segurança de infra-estrutura e selecione **habilitar cópias de segurança automáticas**. Certifique-se definir o período de retenção e a frequência desejado.
    - Ocorrência: Baixa 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicações

<!-- 2352906 - IS ASDK --> 
- Tem de registar o fornecedor de recursos de armazenamento antes de criar a sua primeira função do Azure na subscrição.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização do Azure Stack 1811 partir [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários de ligação apenas, implementações do Azure Stack verificar periodicamente um ponto de extremidade seguro e notificam automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [gerir atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passos Seguintes

- Para rever a política de manutenção para sistemas integrados do Azure Stack e o que deve fazer para manter seu sistema num estado suportado, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para utilizar o ponto final com privilégios (PEP) para monitorizar e retomar as atualizações, consulte [monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios](azure-stack-monitor-update.md).  
- Para uma descrição geral da gestão de atualizações no Azure Stack, veja [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar as atualizações com o Azure Stack, veja [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).  
