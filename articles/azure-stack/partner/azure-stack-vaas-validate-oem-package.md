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
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 5ba290f442f4c27b510538d7c1f7b5e27467efc5
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246658"
---
# <a name="validate-oem-packages"></a>Validar pacotes do OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Quando tiver ocorrido uma alteração para o firmware ou os controladores de uma validação de solução concluída, pode testar um novo pacote de OEM. Quando o pacote tiver passado o teste, ele é assinado pela Microsoft. O teste tem de conter o pacote de extensão de OEM atualizado com os controladores e firmware que tenham passado o logótipo do Windows Server e os testes PCS.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Antes de carregar ou enviar pacotes, consulte [criar um pacote de OEM](azure-stack-vaas-create-oem-package.md) para o formato esperado do pacote e o conteúdo.

## <a name="managing-packages-for-validation"></a>Gerenciamento de pacotes para a validação

Ao utilizar o **validação do pacote** fluxo de trabalho para validar um pacote, terá de fornecer um URL para um **blob de armazenamento do Azure**. Este blob é o pacote de OEM que foi instalado na solução no momento da implementação. Criar o blob com a conta de armazenamento do Azure que criou durante a configuração (consulte [configurar a sua validação como recursos de serviço](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Pré-requisito: Aprovisionar um contentor de armazenamento

Crie um contentor na sua conta de armazenamento para blobs de pacote. Este contentor pode ser utilizado para todas as executa a validação do pacote.

1. Na [Portal do Azure](https://portal.azure.com), vá para a conta de armazenamento criada no [configurar sua validação como recursos de serviço](azure-stack-vaas-set-up-resources.md).
2. No painel esquerdo, em **serviço Blob**, selecione no **contentores**.
3. Selecione **+ contentor** no menu da barra e forneça um nome para o contentor, por exemplo, `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Carregar o pacote para a conta de armazenamento

1. Prepare o pacote que pretende validar. Se o pacote tiver vários ficheiros, comprimir num `.zip` ficheiro.
2. Na [Portal do Azure](https://portal.azure.com), selecione o contentor de pacote e carregar o pacote selecionando na **carregar** na barra de menus.
3. Selecione o pacote `.zip` ficheiro a carregar. Mantenha predefinições **tipo de Blob** (ou seja, **Blob de blocos**) e **Bloquear tamanho**.

> [!NOTE]
> Certifique-se que o `.zip` conteúdo é colocado na raiz do `.zip` ficheiro. Não deve haver nenhum subpastas no pacote.

### <a name="generate-package-blob-url-for-vaas"></a>Gerar o URL do blob de pacote para VaaS

Ao criar um **validação do pacote** fluxo de trabalho no portal do VaaS, terá de fornecer um URL para o blob de armazenamento do Azure que contém o pacote.

#### <a name="option-1-generating-an-account-sas-url"></a>Opção 1: Gerar uma URL de SAS de conta

1. Na [portal do Azure](https://portal.azure.com/), aceda à sua conta de armazenamento e navegue para o. zip que contém o pacote

2. Selecione **gerar SAS** no menu de contexto

3. Selecione **leitura** partir **permissões**

4. Definir **hora de início** para a hora atual, e **hora de fim** , pelo menos, 48 horas **hora de início**. Se executar outros testes com o mesmo pacote, considere aumentar **hora de fim** para o comprimento de teste. Qualquer teste programado pelo VaaS após **hora de fim** irão falhar e uma SAS novo terá de ser gerado.

5. Selecione **gerar o token SAS do blob e o URL**

Uso **URL de SAS do Blob** ao iniciar uma nova **validação do pacote** fluxo de trabalho no portal do VaaS.

#### <a name="option-2-using-public-read-container"></a>Opção 2: Com o contentor de leitura público

> [!CAUTION]
> Esta opção abre o contentor para acesso só de leitura anónimo.

1. Concessão **acesso apenas para blobs de leitura pública** para o contêiner do pacote ao seguir as instruções na secção [conceder permissões de utilizadores anónimos a contentores e blobs](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. No contêiner de pacote, selecione o blob de pacote no contentor para abrir o painel de propriedades.

3. Copiar o **URL**. Utilize este valor ao iniciar uma nova **validação do pacote** fluxo de trabalho no portal do VaaS.

## <a name="apply-monthly-update"></a>Aplicar atualização mensal

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Criar um fluxo de trabalho de validação do pacote

1. Inicie sessão para o [VaaS portal](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selecione **começar** sobre o **validação do pacote** mosaico.

    ![Mosaico de fluxo de trabalho de validações do pacote](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Introduza o URL do blob de armazenamento do Azure para o pacote de OEM que foi instalado na solução no momento da implementação. Para obter instruções, consulte [gerar o URL do blob de pacote para VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parâmetros do ambiente não podem ser modificados depois de criar um fluxo de trabalho.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Será redirecionado para a página de resumo de testes.

## <a name="run-package-validation-tests"></a>Executar testes de validação do pacote

1. Na **resumo de testes de validação do pacote** página, verá uma lista de testes necessários para concluir a validação. Executam testes neste fluxo de trabalho para cerca de 24 horas.

    Em fluxos de trabalho de validação, **agendamento** um teste utiliza os parâmetros comuns de nível de fluxo de trabalho que especificou durante a criação de fluxo de trabalho (consulte [parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md)). Se qualquer um dos valores de parâmetro de teste se torne inválido, deve resupply-los com as instruções na [modifique os parâmetros de fluxo de trabalho](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Um teste de validação de agendamento sobre uma instância existente, irá criar uma nova instância em vez da instância antiga no portal. Registos para a instância antiga serão mantidos, mas não estão acessíveis a partir do portal.  
    Quando um teste tiver sido concluída com êxito, o **agenda** ação torna-se desativada.

2. Selecione o agente que executará o teste. Para informações sobre como adicionar locais agentes de execução de teste, consulte [implementar o agente local](azure-stack-vaas-local-agent.md).

3. Para cada um dos seguintes testes, a etapa quatro e cinco:
    - Verificação do pacote de extensão de OEM
    - Mecanismo de simulação de cloud

4. Selecione **agenda** no menu de contexto para abrir um prompt para agendar a instância de teste.

5. Reveja os parâmetros de teste e, em seguida, selecione **submeter** para agendar o teste para execução.

Quando todos os testes foram concluídas com êxito, enviar o nome da sua solução de VaaS e validação do pacote para [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) para solicitar a assinatura de pacote.

## <a name="next-steps"></a>Passos Seguintes

- [Monitorizar e gerir os testes no portal do VaaS](azure-stack-vaas-monitor-test.md)