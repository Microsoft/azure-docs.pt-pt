---
title: Atualização de 1901 de pilha do Azure | Documentos da Microsoft
description: Saiba mais sobre a atualização de 1901 para o sistema integrado do Azure Stack, incluindo o que há de novo, onde pode transferir a atualização e problemas conhecidos.
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
ms.date: 02/27/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 77b3d8bd1d16e90e9929c41f0f28940694dc7906
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889839"
---
# <a name="azure-stack-1901-update"></a>Atualização de 1901 de pilha do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1901. A atualização inclui melhorias, correções e novas funcionalidades para esta versão do Azure Stack. Este artigo também descreve os problemas conhecidos nesta versão e inclui uma ligação para transferir a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados com o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é apenas para sistemas integrados do Azure Stack. Não é aplicável este pacote de atualização para o Azure Stack Development Kit.

## <a name="build-reference"></a>Criar a referência

É o número de compilação de atualização do Azure Stack 1901 **1.1901.0.95** ou **1.1901.0.99** depois de 26 de Fevereiro de 2019. Consulte a nota do seguinte:

> [!IMPORTANT]  
> Microsoft detetou um problema que pode afetar os clientes que atualizar a partir do 1811 (1.1811.0.101) para 1901 e lançou um pacote de 1901 atualizado para resolver o problema: criar 1.1901.0.99, atualizados a partir do 1.1901.0.95. Não é necessário que os clientes que já tenham sido atualizados para 1.1901.0.95 qualquer ação adicional.
>
> Os clientes ligados que se encontram em 1811 automaticamente verá o novo pacote de 1901 (1.1901.0.99) disponível no portal do administrador e devem instalá-la quando estiver pronto. Os clientes desligados podem transferir e importar o novo pacote de 1901 usando o mesmo processo [descrito aqui](azure-stack-apply-updates.md).
>
> Os clientes com qualquer versão do 1901 não serão influenciados ao instalar o pacote completo ou a correção seguinte.

## <a name="hotfixes"></a>Correções

O Azure Stack lança correções em intervalos regulares. Certifique-se de que instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1811 antes de atualizar o Azure Stack para 1901.

Correções de pilha do Azure só são aplicáveis a sistemas integrados do Azure Stack; Não tente instalar correções no ASDK.

> [!TIP]  
> Subscrever o seguinte procedimento *RSS* ou *Atom* feeds para se manter atualizado com correções do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Correções de pilha do Azure

- **1809**: [KB 4481548 – o Azure Stack correção 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Sem correções atual disponível.
- **1901**: Sem correções atual disponível.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
- Instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1811 (se houver) antes de atualizar para 1901.

- Antes de iniciar a instalação desta atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os parâmetros seguintes para validar o status do seu Azure Stack e resolver quaisquer problemas operacionais detectados, incluindo todos os avisos e falhas. Também rever alertas ativos e resolver qualquer um que requerem uma ação:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

## <a name="new-features"></a>Novos recursos

Esta atualização inclui as seguintes novas funcionalidades e melhorias para o Azure Stack:

- Imagens geridas na ativação do Azure Stack-o a criar um objeto de imagem gerida numa VM generalizada (ambos geridos e não geridos) que só pode criar gerida disco VMs no futuro. Para obter mais informações, consulte [Managed Disks do Azure Stack](user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Correção de erro - `Import-AzureRmContext` desserializar o token guardado corretamente.  
   * **AzureRm.Resources**  
         Correção de erro - `Get-AzureRmResource` para o caso de consulta insensitively por tipo de recurso.  
   * **Azure.Storage**  
         Módulo de rollup do AzureRm agora inclui o suporte da versão já publicada 4.5.0 a **versão de api 2017-07-29**.  
   * **AzureRm.Storage**  
         Módulo de rollup do AzureRm agora inclui o suporte da versão já publicada 5.0.4 a **2017-10-01 a api-version**.  
   * **AzureRm.Compute**  
         Foi adicionado o parâmetro simple define na `New-AzureRMVM` e `NewAzureRMVMSS`, `-ImageName` parâmetro dá suporte a especificação de utilizador de imagens.  
   * **AzureRm.Insights**  
         Módulo de rollup do AzureRm agora inclui o suporte da versão já publicada 5.1.5 a **versão de api de 2018-01-01** para métricas, tipos de recursos de definições de métricas.

- **AzureStack 1.7.0** isso uma alteração de última hora de versão. Para obter detalhes sobre as alterações de última hora, consulte https://aka.ms/azspshmigration170
   * **Módulo de Azs.Backup.Admin**  
         Alteração significativa: Cópia de segurança muda para o modo de encriptação baseada em certificado. Suporte para chaves simétricas é preterido.  
   * **Módulo de Azs.Fabric.Admin**  
         `Get-AzsInfrastructureVolume` foi preterido. Utilizar o novo cmdlet `Get-AzsVolume`.  
         `Get-AzsStorageSystem` foi preterido.  Utilizar o novo cmdlet `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` foi preterido. O `StorageSubSystem` objeto contém a propriedade de capacidade.  
   * **Módulo de Azs.Compute.Admin**  
         Correção de erro - `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: Chamar `ConvertTo-PlatformImageObject` apenas no caminho do sucesso.  
         BugFix - `Add-AzsVmExtension`, `Get-AzsVmExtension`: Chamando ConvertTo-VmExtensionObject apenas no caminho do sucesso.  
   * **Módulo de Azs.Storage.Admin**  
         Correção de erro - nova Quota de armazenamento utiliza as predefinições, se não tiver nenhuma fornecido.

Para rever a referência para os módulos atualizados, consulte [referência de módulo do Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Problemas corrigidos

- Foi corrigido um problema no qual o portal mostrou uma opção para criar gateways do VPN baseada em políticas que não são suportados no Azure Stack. Esta opção foi removida do portal.

<!-- 16523695 – IS, ASDK -->
- Foi corrigido um problema em que depois de atualizar as definições de DNS para a sua rede Virtual a partir **DNS do uso do Azure Stack** ao **DNS personalizado**, as instâncias não foram atualizadas com a nova definição.

- <!-- 3235634 – IS, ASDK --> Foi corrigido um problema no qual implementar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especificando o sufixo como obrigatório **Standard_A2_v2** ( v em minúsculas). Como com o global Azure, pode agora utilizar **Standard_A2_V2** (V maiúsculo).

<!-- 2869209 – IS, ASDK --> 
- Foi corrigido um problema ao utilizar o [cmdlet Add-AzsPlatformImage](/powershell/module/azs.compute.admin/add-azsplatformimage), no qual tinha de utilizar o **- OsUri** parâmetro como a conta de armazenamento URI onde o disco é carregado. Agora, pode também utilizar o caminho local para o disco.

<!--  2795678 – IS, ASDK --> 
- Foi corrigido um problema que produziu um aviso quando utilizou o portal para criar máquinas virtuais (VMs) num tamanho VM premium (DS, Ds_v2, FS, FSv2). A VM foi criada na conta de armazenamento standard. Embora isso não afetou funcionalmente, IOPs, ou de faturação, o aviso foi corrigido.

<!-- 1264761 - IS ASDK -->  
- Foi corrigido um problema com o **controlador de estado de funcionamento** componente que estava gerando os seguintes alertas. Os alertas podem ser ignorados com segurança:

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


<!-- 3507629 - IS, ASDK --> 
- Foi corrigido um problema ao definir o valor de quotas de Managed Disks sob [tipos de quota de computação](azure-stack-quota-types.md#compute-quota-types) como 0, é equivalente para o valor predefinido de 2048 GiB. O valor da quota zero agora é respeitado.

<!-- 2724873 - IS --> 
- Foi corrigido um problema ao utilizar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleUnitNode** para gerir unidades de escala, em que a primeira tentativa para iniciar ou parar a unidade de escala pode falhar.

<!-- 2724961- IS ASDK --> 
- Foi corrigido um problema em que registrou a **Microsoft.Insight** fornecedor de recursos nas definições de subscrição e criado uma VM do Windows com o convidado SO diagnóstico ativado, mas não foi exibido o gráfico de percentagem de CPU na página de descrição geral da VM dados de métricas. Os dados agora exiba corretamente.

- Foi corrigido um problema no qual executar o **Get-AzureStackLog** cmdlet falhou após a execução **teste AzureStack** na mesma sessão de ponto final com privilégios (PEP). Agora, pode utilizar a mesma sessão PEP em que é executado **AzureStack teste**.

<!-- bug 3615401, IS -->
- Foi corrigido o problema com cópias de segurança automáticas, onde o serviço de agendador deveria Ir para o estado desativado inesperadamente. 

<!--2850083, IS ASDK -->
- Removida a **repor o Gateway** botão a partir do portal do Azure Stack, que apresentou um erro se o botão foi clicado. Este botão não serve nenhuma função no Azure Stack, como o Azure Stack tem um gateway de multi-inquilino, em vez de instâncias VM dedicadas para cada inquilino Gateway de VPN, para que ele foi removido para evitar confusão. 

<!-- 3209594, IS ASDK -->
- Removida a **regras de segurança efetivas** uma ligação a **propriedades do sistema de rede** painel como esta funcionalidade não é suportado no Azure Stack. Ter a ligação presente deu a impressão de que esta funcionalidade foi suportada, mas não está a funcionar. Para minimizar a confusão, removemos a ligação.

<!-- 3139614 | IS -->
- Foi corrigido um problema em que, após uma atualização foi aplicada ao Azure Stack de um OEM, a **atualização disponível** notificação não sejam apresentados no portal de administrador do Azure Stack.

## <a name="changes"></a>Alterações

<!-- 3083238 IS -->
- Aprimoramentos de segurança nesta atualização resultam num aumento no tamanho da função de serviço de diretório de cópia de segurança. Para atualizada a documentação de orientação para a localização de armazenamento externo de dimensionamento, veja a [documentação de cópia de segurança de infra-estrutura](azure-stack-backup-reference.md#storage-location-sizing). Esta alteração resulta em mais tempo para concluir a cópia de segurança devido a transferência de dados de tamanho maior. Esta alteração irá afetar os sistemas integrados. 

- A partir de Janeiro de 2019, pode implementar clusters do Kubernetes no Active Directory Federated Services (AD FS) registados, ligada do Azure Stack carimbos de data / (acesso à internet é necessário a maiúsculas e minúsculas). Siga as instruções [aqui](azure-stack-solution-template-kubernetes-cluster-add.md) para baixar o novo item do Marketplace de Kubernetes. Siga as instruções [aqui](user/azure-stack-solution-template-kubernetes-adfs.md) para implementar um cluster de Kubernetes. Tenha em atenção os novos parâmetros para que indica se o sistema de destino está a adicionar ou o AD FS registado. Caso se trate do AD FS, novos campos estão disponíveis para inserir os parâmetros de Cofre de chaves no qual o certificado de implementação é armazenado.

   Observe que, mesmo com suporte para AD FS, a implementação de clusters de Kubernetes requer acesso à internet.

- Depois de instalar atualizações ou correções para o Azure Stack, poderão ser introduzidas novas funcionalidades que necessitam de novas permissões para ser concedida a um ou mais aplicações de identidade. Conceder estas permissões requer acesso administrativo para o diretório raiz e, portanto, ele não pode ser feito automaticamente. Por exemplo:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Há uma consideração de novo para planeamento da capacidade do Azure Stack com precisão. Com a atualização de 1901, agora existe um limite no número total de máquinas virtuais que podem ser criados.  Este limite é de se destina a ser temporário para evitar instabilidade de solução. A origem do problema de estabilidade no número mais elevado de VMs que está a ser resolvida, mas ainda não foram identificada uma linha de tempo específica de remediação. Com a atualização de 1901, agora existe um limite de servidor de 60 VMs com um limite de solução total de 700 por.  Por exemplo, um limite de VM do Azure Stack 8 server seria 480 (8 * 60).  Para um servidor de 12 a 16 solução do Azure Stack, o limite seria 700. Este limite foi criado tendo a toda a computação considerações sobre a capacidade em mente, como a reserva de resiliência e a CPU virtual para física proporção que um operador gostaria de manter o carimbo. Para obter mais informações, veja a nova versão do Planeador de capacidade.  
No caso de foi atingido o limite de dimensionamento VM, os seguintes códigos de erro seriam retornados assim: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- A versão de API de computação tem aumentado para 2017-12-01.

- Cópia de segurança da infraestrutura agora requer um certificado com apenas uma chave pública (. CER) para encriptação de dados de cópia de segurança. Suporte de chave de encriptação simétrica foi preterido a partir de 1901. Se a cópia de segurança de infra-estrutura estiver configurada antes de atualizar para 1901, as chaves de encriptação permanecerão em vigor. Terá de, pelo menos, 2 mais atualiza com efeitos suporte de compatibilidade para atualizar as definições de cópia de segurança. Para obter mais informações, consulte [práticas recomendadas do Azure Stack infraestrutura cópia de segurança](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as seguintes atualizações de segurança:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Para obter mais informações sobre essas vulnerabilidades, clique nos links anteriores ou veja os artigos da Base de dados de conhecimento da Microsoft [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando em execução [teste AzureStack](azure-stack-diagnostic-test.md), se qualquer um da **AzsInfraRoleSummary** ou o **AzsPortalApiSummary** teste falhar, se lhe for pedido para executar  **Teste AzureStack** com o `-Repair` sinalizador.  Se executar este comando, ocorre uma falha com a seguinte mensagem de erro:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- Quando executa [AzureStack teste](azure-stack-diagnostic-test.md), será apresentada uma mensagem de aviso do controlador de gestão de placas base (BMC). Pode ignorar este aviso.

- <!-- 2468613 - IS --> Durante a instalação desta atualização, poderá ver alertas com o título `Error – Template for FaultType UserAccounts.New is missing.` pode ignorar com segurança estes alertas. Os alertas fechar automaticamente depois de concluída a instalação desta atualização.

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

<!-- 3557860 - IS ASDK --> 
- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos inteiro e, em seguida, elimine as subscrições de utilizador.

<!-- 1663805 - IS ASDK --> 
- Não é possível ver as permissões à sua subscrição a utilizar os portais do Azure Stack. Como solução, utilize [PowerShell para verificar as permissões](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Computação

- Ao criar um novo Windows Máquina Virtual (VM), poderá ser apresentado o erro seguinte:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   O erro ocorre se ativar diagnósticos de arranque numa VM, mas eliminar a sua conta de armazenamento de diagnósticos de arranque. Para contornar este problema, recrie a conta de armazenamento com o mesmo nome que utilizou anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A experiência de criação de conjunto (VMSS) de dimensionamento de máquina virtual fornece 7.2 baseada em CentOS, como uma opção para implementação. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para a sua implementação, ou utilizar um modelo do Azure Resource Manager, especificando outra imagem do CentOS que tenha sido transferida antes da implementação a partir do mercado, o operador.  

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1901 de atualização, poderá encontrar os seguintes problemas ao implementar VMs com discos geridos:

   - Se a subscrição tiver sido criada antes da atualização de 1808, implementar uma VM com Managed Disks poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos para cada subscrição:
      1. No portal do inquilino, aceda a **subscrições** e encontrar a subscrição. Selecione **fornecedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **voltar a registar**.
      2. Na mesma subscrição, aceda a **controlo de acesso (IAM)** e certifique-se de que **AzureStack-DiskRP-Client** está listado.
   - Se tiver configurado o ambiente multi-inquilino, a implementação de VMs numa assinatura associada um diretório de convidado poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos [este artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) reconfigurar cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização de SSH ativada não permitirá que use as chaves SSH para iniciar sessão. Como solução, utilize o acesso VM para a extensão do Linux para implementar as chaves SSH após o aprovisionamento ou utilizar a autenticação baseada em palavra-passe.

### <a name="networking"></a>Redes  

<!-- 3239127 - IS, ASDK -->
- No portal do Azure Stack, quando altera um endereço IP estático para uma configuração de IP que está vinculado a um adaptador de rede anexado a uma instância VM, verá uma mensagem de aviso que indica 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Pode ignorar esta mensagem; o endereço IP será alterado, mesmo que a instância VM não é reinicializado.

<!-- 3632798 - IS, ASDK -->
- No portal, se adicionar uma regra de segurança de entrada e selecione **etiquetas de serviço** como a origem, várias opções são apresentadas no **marca de origem** lista que não estão disponíveis para o Azure Stack. As únicas opções que são válidas no Azure Stack são os seguintes:

    - **Internet**
    - **VirtualNetwork**
    - **AzureLoadBalancer**
  
    As outras opções não são suportadas como etiquetas de origem no Azure Stack. Da mesma forma, se adicionar uma regra de segurança de saída e selecione **etiquetas de serviço** como o destino, a mesma lista de opções para **marca de origem** é apresentado. As opções apenas válidas são os mesmos que para **marca de origem**, conforme descrito na lista anterior.

- Grupos de segurança de rede (NSGs) não funcionam no Azure Stack estar na mesma forma como global. No Azure, pode definir várias portas numa regra NSG (com o portal, PowerShell e modelos do Resource Manager). No Azure Stack, no entanto, não é possível definir várias portas numa regra NSG através do portal. Para contornar este problema, utilize um modelo do Resource Manager ou o PowerShell para definir estas regras adicionais.

<!-- 3203799 - IS, ASDK -->
- O Azure Stack não suporta a ligação a mais de 4 Interfaces de rede (NICs) para as instâncias VM de hoje em dia, independentemente do tamanho de instância.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicações

<!-- 2352906 - IS ASDK --> 
- Tem de registar o fornecedor de recursos de armazenamento antes de criar a sua primeira função do Azure na subscrição.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização do Azure Stack 1901 partir [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários de ligação apenas, implementações do Azure Stack verificar periodicamente um ponto de extremidade seguro e notificam automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [gerir atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral da gestão de atualizações no Azure Stack, veja [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar as atualizações com o Azure Stack, veja [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).
- Para rever a política de manutenção para sistemas integrados do Azure Stack e o que deve fazer para manter seu sistema num estado suportado, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para utilizar o ponto final com privilégios (PEP) para monitorizar e retomar as atualizações, consulte [monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios](azure-stack-monitor-update.md).  
