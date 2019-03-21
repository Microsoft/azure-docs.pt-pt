---
title: Validar pacotes de fabricante de equipamento original (OEM) na validação de pilha do Azure como um serviço | Documentos da Microsoft
description: Saiba como validar pacotes de fabricante de equipamento original (OEM) com a validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113287"
---
# <a name="validate-oem-packages"></a>Validar pacotes do OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Quando tiver ocorrido uma alteração para o firmware ou os controladores de uma validação de solução concluída, pode testar um novo pacote de OEM. Quando o pacote tiver passado o teste, ele é assinado pela Microsoft. O teste tem de conter o pacote de extensão de OEM atualizado com os controladores e firmware que tenham passado o logótipo do Windows Server e os testes PCS.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Antes de carregar ou enviar pacotes, consulte [criar um pacote de OEM](azure-stack-vaas-create-oem-package.md) para o formato esperado do pacote e o conteúdo.

## <a name="managing-packages-for-validation"></a>Gerenciamento de pacotes para a validação

Ao utilizar o **validação do pacote** fluxo de trabalho para validar um pacote, terá de fornecer um URL para um **blob de armazenamento do Azure**. Este blob é que o teste assinado o pacote de OEM que será instalado como parte do processo de atualização. Criar o blob com a conta de armazenamento do Azure que criou durante a configuração (consulte [configurar a sua validação como recursos de serviço](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Pré-requisito: Aprovisionar um contentor de armazenamento

Crie um contentor na sua conta de armazenamento para blobs de pacote. Este contentor pode ser utilizado para todas as executa a validação do pacote.

1. Na [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento criada no [configurar sua validação como recursos de serviço](azure-stack-vaas-set-up-resources.md).

2. No painel esquerdo, em **serviço Blob**, selecione **contentores**.

3. Selecione **+ contentor** na barra de menus.
    1. Forneça um nome para o contentor, por exemplo, `vaaspackages`.
    1. Selecione o nível de acesso desejado para clientes não autenticados, como de VaaS. Para obter detalhes sobre como pode conceder acesso de VaaS pacotes em cada cenário, consulte [lidar com o nível de acesso do contentor](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Carregar o pacote para a conta de armazenamento

1. Prepare o pacote que pretende validar. Este é um `.zip` ficheiros cujo conteúdo tem de coincidir com a estrutura descrita na [criar um pacote de OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Certifique-se que o `.zip` conteúdo é colocado na raiz do `.zip` ficheiro. Não deve haver nenhum subpastas no pacote.

1. Na [portal do Azure](https://portal.azure.com), selecione o contentor de pacote e carregar o pacote selecionando na **carregar** na barra de menus.

1. Selecione o pacote `.zip` ficheiro a carregar. Mantenha predefinições **tipo de Blob** (ou seja, **Blob de blocos**) e **Bloquear tamanho**.

### <a name="generate-package-blob-url-for-vaas"></a>Gerar o URL do blob de pacote para VaaS

Ao criar um **validação do pacote** fluxo de trabalho no portal do VaaS, terá de fornecer um URL para o blob de armazenamento do Azure que contém o pacote. Algumas *interativo* testes, incluindo **verificação de atualização mensal do AzureStack** e **verificação de pacote de extensão de OEM**, também requer uma URL para blobs de pacote.

#### <a name="handling-container-access-level"></a>Nível de acesso do contentor de tratamento

O nível de acesso mínimo necessário para VaaS depende se estiver a criar um fluxo de trabalho de validação do pacote ou a agendamento de um *interativo* testar.

No caso da **privada** e **Blob** níveis, de acesso temporariamente tem de conceder acesso para o blob de pacote, dando VaaS um [assinatura de acesso partilhado](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). O **contentor** nível de acesso necessita de gerar SAS URLs, mas permite o acesso não autenticado para o contentor e respetivos blobs.

|Nível de acesso | Requisito de fluxo de trabalho | Requisito de teste |
|---|---------|---------|
|Privado | Gerar um URL de SAS por blob de pacote ([opção 1](#option-1-generate-a-blob-sas-url)). | Gerar um URL de SAS ao nível da conta e adicionar manualmente o nome de blob do pacote ([opção 2](#option-2-construct-a-container-sas-url)). |
|Blobs | Fornecer a propriedade de URL do blob ([opção 3](#option-3-grant-public-read-access)). | Gerar um URL de SAS ao nível da conta e adicionar manualmente o nome de blob do pacote ([opção 2](#option-2-construct-a-container-sas-url)). |
|Contentor | Fornecer a propriedade de URL do blob ([opção 3](#option-3-grant-public-read-access)). | Fornecer a propriedade de URL do blob ([opção 3](#option-3-grant-public-read-access)).

As opções para conceder acesso a seus pacotes são ordenadas de acesso mínimo para maior acesso.

#### <a name="option-1-generate-a-blob-sas-url"></a>Opção 1: Gerar um URL de SAS do blob

Utilize esta opção se o nível de acesso do seu contentor de armazenamento é definido como **privada**, onde o contentor não ativa o acesso de leitura público para o contentor ou os respetivos blobs.

> [!NOTE]
> Este método não funcionará para *interativo* testes. Consulte [opção 2: Construir um URL de SAS do contentor](#option-2-construct-a-container-sas-url).

1. Na [portal do Azure](https://portal.azure.com/), aceda à sua conta de armazenamento e navegue para o. zip que contém o pacote.

2. Selecione **gerar SAS** no menu de contexto.

3. Selecione **leitura** partir **permissões**.

4. Definir **hora de início** para a hora atual, e **hora de fim** , pelo menos, 48 horas **hora de início**. Se criar outros fluxos de trabalho com o mesmo pacote, considere aumentar **hora de fim** para o comprimento de teste.

5. Selecione **gerar o token SAS do blob e o URL**.

Utilize o **URL de SAS do Blob** quando fornecer pacote blob URLs para o portal.

#### <a name="option-2-construct-a-container-sas-url"></a>Opção 2: Construir um URL de SAS do contentor

Utilize esta opção se o nível de acesso do seu contentor de armazenamento é definido como **privada** e tem de fornecer um URL do blob de pacote para uma *interativas* testar. Este URL também pode servir-se ao nível do fluxo de trabalho.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Selecione **Blob** partir **opções de serviços permitidos**. Anular seleção de quaisquer opções restantes.

1. Selecione **contentor** e **objeto** partir **tipos de recurso permitidos**.

1. Selecione **leitura** e **lista** partir **permissões**. Anular seleção de quaisquer opções restantes.

1. Selecione **hora de início** como a hora atual e **hora de fim** para, pelo menos, de 14 dias da **hora de início**. Se executar outros testes com o mesmo pacote, considere aumentar **hora de fim** para o comprimento de teste. Qualquer teste programado pelo VaaS após **hora de fim** irão falhar e uma SAS novo terá de ser gerado.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    O formato deve ser apresentado da seguinte forma: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modificar o URL de SAS gerado para incluir o contêiner do pacote `{containername}`e o nome do seu blob de pacote, `{mypackage.zip}`, da seguinte forma:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Utilize este valor ao fornecer o pacote do blob URLs para o portal.

#### <a name="option-3-grant-public-read-access"></a>Opção 3: Conceder acesso de leitura público

Utilize esta opção se for aceitável para permitir que os clientes não autenticados acesso para blobs individuais ou, no caso da *interativo* os testes, o contentor.

> [!CAUTION]
> Esta opção abre seu blob (s) para acesso só de leitura anónimo.

1. Definir o nível de acesso do contentor como pacote **Blob** ou **contentor** ao seguir as instruções na secção [conceder permissões de utilizadores anónimos a contentores e blobs](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Se está a fornecer um URL de pacote para uma *interativo* teste, tem de conceder **total acesso de leitura público** para o contentor para continuar com o teste.

1. No contêiner de pacote, selecione o blob de pacote para abrir o painel de propriedades.

1. Copiar o **URL**. Utilize este valor ao fornecer o pacote do blob URLs para o portal.

## <a name="create-a-package-validation-workflow"></a>Criar um fluxo de trabalho de validação do pacote

1. Inicie sessão para o [VaaS portal](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selecione **começar** sobre o **validação do pacote** mosaico.

    ![Mosaico de fluxo de trabalho de validações do pacote](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Introduza o armazenamento do Azure a URL do blob para o teste assinado o pacote de OEM que requerem uma assinatura da Microsoft. Para obter instruções, consulte [gerar o URL do blob de pacote para VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parâmetros do ambiente não podem ser modificados depois de criar um fluxo de trabalho.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Será redirecionado para a página de resumo de testes.

## <a name="required-tests"></a>Testes necessários

Os seguintes testes são necessários para validação do OEM do pacote:

- Verificação do pacote de extensão de OEM
- Mecanismo de simulação de cloud

## <a name="run-package-validation-tests"></a>Executar testes de validação do pacote

1. Na **resumo de testes de validação do pacote** página, executará um subconjunto dos testes listados adequados ao seu cenário.

    Em fluxos de trabalho de validação, **agendamento** um teste utiliza os parâmetros comuns de nível de fluxo de trabalho que especificou durante a criação de fluxo de trabalho (consulte [parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md)). Se qualquer um dos valores de parâmetro de teste se torne inválido, deve resupply-los com as instruções na [modifique os parâmetros de fluxo de trabalho](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Um teste de validação de agendamento sobre uma instância existente, irá criar uma nova instância em vez da instância antiga no portal. Registos para a instância antiga serão mantidos, mas não estão acessíveis a partir do portal.  
    > Quando um teste tiver sido concluída com êxito, o **agenda** ação torna-se desativada.

2. Selecione o agente que executará o teste. Para informações sobre como adicionar locais agentes de execução de teste, consulte [implementar o agente local](azure-stack-vaas-local-agent.md).

3. Para concluir a verificação de pacote de extensão de OEM, selecione **agenda** no menu de contexto para abrir um prompt para agendar a instância de teste.

4. Reveja os parâmetros de teste e, em seguida, selecione **submeter** para agendar a verificação de pacote de extensão do OEM para execução.

    Verificação de pacote de extensão de OEM é dividida em dois passos manuais: O Azure Stack Update e atualização do OEM.

   1. **Selecione** "Executar" na IU para executar o script precheck. Este é um teste automatizado que demora cerca de 5 minutos a concluir e não requer nenhuma ação.

   1. Depois de concluído o script precheck, executar o passo manual: **instalar** a atualização mais recente disponível do Azure Stack através do portal do Azure Stack.

   1. **Executar** AzureStack de teste no carimbo. Se ocorrerem falhas, não continue com o teste e contact [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

       Para obter informações sobre como executar o comando de teste AzureStack, consulte [estado do sistema de validar o Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

   1. **Selecione** "Seguinte" para executar o script postcheck. Este é um teste automatizado e marca o fim do processo de atualização do Azure Stack.

   1. **Selecione** "Executar" para executar o script precheck para atualização do OEM.

   1. Depois de concluída a pré-verificação, executar o passo manual: **instalar** o pacote de extensão de OEM através do portal.

   1. **Executar** AzureStack de teste no carimbo.

      > [!NOTE]
      > Como antes, não continue com o teste e contact [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) se falhar. Este passo é crítico, uma vez que isso vai economizar uma reimplantação.

   1. **Selecione** "Seguinte" para executar o script postcheck. Isto marca o fim do passo de atualização de OEM.

   1. Responder a quaisquer questões restantes no final do teste e **selecione** "Submeter".

   1. Isto marca o fim do teste interativo.

5. Reveja o resultado para a verificação de pacote de extensão do OEM. Assim que o teste foi bem sucedida, agende o mecanismo de simulação de Cloud para execução.

Para submeter um pedido de assinatura do pacote, envie [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) o nome da solução e o nome de validação do pacote associado a esta execução.

## <a name="next-steps"></a>Passos Seguintes

- [Monitorizar e gerir os testes no portal do VaaS](azure-stack-vaas-monitor-test.md)
