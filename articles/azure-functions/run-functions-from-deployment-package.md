---
title: Executar o Azure Functions de um pacote | Microsoft Docs
description: Faça com que o tempo de execução de Azure Functions execute suas funções montando um arquivo de pacote de implantação que contém os arquivos de projeto do aplicativo de funções.
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 96dbe9b15831a349afc0e68c15c39c1cb31b1032
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444058"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Executar o Azure Functions de um arquivo de pacote

No Azure, você pode executar suas funções diretamente de um arquivo de pacote de implantação em seu aplicativo de funções. A outra opção é implantar seus arquivos no `d:\home\site\wwwroot` diretório do seu aplicativo de funções.

Este artigo descreve os benefícios da execução de suas funções a partir de um pacote. Ele também mostra como habilitar essa funcionalidade em seu aplicativo de funções.

> [!IMPORTANT]
> Ao implantar suas funções em um aplicativo de funções do Linux em um [plano Premium](functions-scale.md#premium-plan), você deve sempre executar a partir do arquivo de pacote e [publicar seu aplicativo usando o Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios da execução de um arquivo de pacote
  
Há vários benefícios em executar a partir de um arquivo de pacote:

+ Reduz o risco de problemas de bloqueio de cópia de arquivo.
+ Pode ser implantado em um aplicativo de produção (com reinicialização).
+ Você pode ter alguns arquivos em execução em seu aplicativo.
+ Melhora o desempenho de implantações de [Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir os horários de início frio, especialmente para funções de JavaScript com árvores de pacote NPM de grande porte.

Para obter mais informações, consulte [este comunicado](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Habilitando funções para execução a partir de um pacote

Para permitir que seu aplicativo de funções seja executado a partir de um pacote, `WEBSITE_RUN_FROM_PACKAGE` basta adicionar uma configuração às configurações do aplicativo de funções. A `WEBSITE_RUN_FROM_PACKAGE` configuração pode ter um dos seguintes valores:

| Value  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicativos de funções em execução no Windows. Execute de um arquivo de pacote na `d:\home\data\SitePackages` pasta do seu aplicativo de funções. Se não estiver implantando [com a implantação de zip](#integration-with-zip-deployment), essa opção exigirá que a pasta `packagename.txt`também tenha um arquivo chamado. Esse arquivo contém apenas o nome do arquivo de pacote na pasta, sem nenhum espaço em branco. |
|**`<url>`**  | Local de um arquivo de pacote específico que você deseja executar. Ao usar o armazenamento de BLOBs, você deve usar um contêiner privado com uma [SAS (assinatura de acesso compartilhado)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o tempo de execução do Functions acesse o pacote. Você pode usar o [Gerenciador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote na sua conta de armazenamento de BLOBs.         |

> [!CAUTION]
> Ao executar um aplicativo de funções no Windows, a opção de URL externa gera um pior desempenho de inicialização a frio. Ao implantar seu aplicativo de funções no Windows, você deve definir `WEBSITE_RUN_FROM_PACKAGE` como `1` e publicar com a implantação de zip.

O seguinte mostra um aplicativo de funções configurado para ser executado a partir de um arquivo. zip hospedado no armazenamento de BLOBs do Azure:

![Configuração do aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Atualmente, há suporte apenas para arquivos de pacote. zip.

## <a name="integration-with-zip-deployment"></a>Integração com a implantação de zip

A [implantação de zip][Zip deployment for Azure Functions] é um recurso do serviço de Azure app que permite que você implante seu projeto `wwwroot` de aplicativo de funções no diretório. O projeto é empacotado como um arquivo de implantação. zip. As mesmas APIs podem ser usadas para implantar o pacote `d:\home\data\SitePackages` na pasta. Com o `WEBSITE_RUN_FROM_PACKAGE` valor de configuração do `1`aplicativo de, as APIs de implantação zip copiam `d:\home\data\SitePackages` o pacote para a pasta em vez de `d:\home\site\wwwroot`extrair os arquivos para o. Ele também cria o `packagename.txt` arquivo. O aplicativo de funções é então executado a partir do pacote após uma reinicialização e `wwwroot` torna-se somente leitura. Para obter mais informações sobre a implantação de zip, consulte [implantação de zip para Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Adicionando a configuração WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Resolução de problemas

- Executar do pacote torna `wwwroot` -se somente leitura, portanto, você receberá um erro ao gravar arquivos nesse diretório.
- Não há suporte para formatos tar e gzip.
- Esse recurso não compõe o cache local.
- Para obter um desempenho de inicialização a frio aprimorado, use a`WEBSITE_RUN_FROM_PACKAGE`opção de zip local (= 1).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
