---
title: Execute as suas funções do Azure a partir de um pacote | Documentos da Microsoft
description: Ter o tempo de execução de funções do Azure, executar as suas funções ao montar um ficheiro de pacote de implementação que contém os ficheiros de projeto de aplicação de função.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: glenga
ms.openlocfilehash: 292b25987f183df2091667312d4e6730b7f40dda
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990905"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Execute as suas funções do Azure a partir de um ficheiro de pacote

> [!NOTE]
> A funcionalidade descrita neste artigo não está disponível para aplicações de funções em execução no Linux numa [plano do App Service](functions-scale.md#app-service-plan).

No Azure, pode executar as suas funções diretamente a partir de um ficheiro de pacote de implementação na sua aplicação de função. A outra opção é implementar os ficheiros no `d:\home\site\wwwroot` diretório da sua aplicação de função.

Este artigo descreve os benefícios de executar as suas funções de um pacote. Ele também mostra como ativar esta funcionalidade na sua aplicação de função.

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios da execução de um ficheiro de pacote
  
Existem várias vantagens para executar a partir de um ficheiro de pacote:

+ Reduz o risco de problemas de bloqueio de cópia de ficheiros.
+ Podem ser implementados para um aplicativo de produção (com reinício).
+ É possível ter certeza dos ficheiros que estão em execução na sua aplicação.
+ Melhora o desempenho das [implementações do Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir os tempos de arranque a frio, particularmente para funções de JavaScript com npm grandes árvores de pacote.

Para obter mais informações, consulte [este anúncio](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Ativar as funções para executar a partir de um pacote

Para ativar a sua aplicação de funções para execução a partir de um pacote, basta adicionar uma `WEBSITE_RUN_FROM_PACKAGE` na definição de suas definições de aplicação de função. O `WEBSITE_RUN_FROM_PACKAGE` definição pode ter um dos seguintes valores:

| Value  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicações de funções em execução no Windows. Execute a partir de um ficheiro de pacote no `d:\home\data\SitePackages` pasta da sua aplicação de função. Se não for [implementar com o zip implementar] (#integration-com-zip-implementação], esta opção requer que a pasta para também tem um arquivo chamado `packagename.txt`. Este ficheiro contém apenas o nome do ficheiro de pacote na pasta, sem quaisquer espaços em branco. |
|**`<url>`**  | Localização de um ficheiro de pacote específico que pretende executar. Ao utilizar o armazenamento de BLOBs, deve usar um contêiner privado com um [assinatura de acesso partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) para ativar o runtime das funções aceder ao pacote. Pode utilizar o [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para carregar ficheiros de pacote para a conta de armazenamento de Blobs.         |

> [!CAUTION]
> Ao executar uma aplicação de funções no Windows, a opção de URL externa produz pior desempenho de arranque a frio. Ao implementar a aplicação de funções no Windows, deve definir `WEBSITE_RUN_FROM_PACKAGE` para `1` e publicar com a implementação do zip.

O código a seguir mostra uma aplicação de funções configurada para ser executado a partir de um ficheiro. zip alojado no armazenamento de Blobs do Azure:

![WEBSITE_RUN_FROM_ZIP app setting](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Atualmente, são suportados apenas ficheiros do pacote. zip.

## <a name="integration-with-zip-deployment"></a>Integração com a implementação de zip

[Zip implantação] [ Zip deployment for Azure Functions] é uma funcionalidade do serviço de aplicações do Azure que permite-lhe implementar o projeto de aplicação de função para o `wwwroot` diretório. O projeto é empacotado como um ficheiro de implementação. zip. As mesmas APIs que pode ser utilizadas para implementar o seu pacote para o `d:\home\data\SitePackages` pasta. Com o `WEBSITE_RUN_FROM_PACKAGE` valor de definição de aplicação do `1`, a implementação de zip APIs copiar o pacote para o `d:\home\data\SitePackages` pasta em vez de extrair os ficheiros para `d:\home\site\wwwroot`. Ele também cria o `packagename.txt` ficheiro. A aplicação de função for executada, em seguida, do pacote após um reinício, e `wwwroot` torna-se só de leitura. Para obter mais informações sobre a implantação do zip, consulte [Zip de implementação para as funções do Azure](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Adicionar a definição de WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
