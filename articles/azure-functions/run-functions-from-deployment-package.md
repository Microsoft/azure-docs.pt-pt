---
title: Execute as suas Funções Azure a partir de um pacote
description: Faça com que as Funções Azure executem as suas funções montando um ficheiro de pacote de implementação que contenha os ficheiros do projeto da aplicação de funções.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365276"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Execute as suas Funções Azure a partir de um ficheiro de pacote

No Azure, pode executar as suas funções diretamente a partir de um ficheiro de pacote de implementação na sua aplicação de função. A outra opção é implementar `d:\home\site\wwwroot` os seus ficheiros no diretório da sua aplicação de funções.

Este artigo descreve os benefícios de executar as suas funções a partir de um pacote. Também mostra como ativar esta funcionalidade na sua aplicação de funções.

> [!IMPORTANT]
> Ao implementar as suas funções numa aplicação de função Linux num [plano Premium,](functions-scale.md#premium-plan)deve sempre executar a partir do ficheiro de pacote e publicar a [sua aplicação utilizando as Ferramentas Core funções do Azure](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios de correr a partir de um ficheiro de pacote
  
Existem vários benefícios para correr a partir de um ficheiro de pacote:

+ Reduz o risco de problemas de bloqueio de cópias de ficheiros.
+ Pode ser implantado numa aplicação de produção (com reinício).
+ Pode ter a certeza dos ficheiros que estão a ser recorridos na sua aplicação.
+ Melhora o desempenho das implementações do Gestor de [Recursos Azure.](functions-infrastructure-as-code.md)
+ Pode reduzir os tempos de arranque a frio, particularmente para funções JavaScript com grandes árvores de embalagem npm.

Para mais informações, consulte [este anúncio.](https://github.com/Azure/app-service-announcements/issues/84)

## <a name="enabling-functions-to-run-from-a-package"></a>Permitindo que as funções funcionem a partir de um pacote

Para permitir que a sua aplicação de `WEBSITE_RUN_FROM_PACKAGE` funções seja executada a partir de um pacote, basta adicionar uma definição às definições da sua aplicação de função. A `WEBSITE_RUN_FROM_PACKAGE` definição pode ter um dos seguintes valores:

| Valor  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicações de funções em execução no Windows. Faça a partir de `d:\home\data\SitePackages` um ficheiro de pacote na pasta da sua aplicação de função. Se não [for implantada com a implementação](#integration-with-zip-deployment)do zip, esta opção requer que a pasta também tenha um ficheiro nomeado `packagename.txt`. Este ficheiro contém apenas o nome do ficheiro de embalagem na pasta, sem qualquer espaço branco. |
|**`<URL>`**  | Localização de um ficheiro de pacote específico que pretende executar. Ao utilizar o armazenamento Blob, deve utilizar um recipiente privado com uma Assinatura de [Acesso Partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que o tempo de funcionamento das funções tenha acesso à embalagem. Pode utilizar o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para fazer o upload de ficheiros de pacotes para a sua conta de armazenamento Blob. Quando especifica um URL, também deve [sincronizar os gatilhos](functions-deployment-technologies.md#trigger-syncing) depois de publicar um pacote atualizado. |

> [!CAUTION]
> Ao executar uma aplicação de função no Windows, a opção DEURL externa produz pior desempenho de arranque a frio. Ao implementar a sua aplicação de `WEBSITE_RUN_FROM_PACKAGE` `1` funções para o Windows, deve definir e publicar com a implementação zip.

O seguinte mostra uma aplicação de função configurada para ser executada a partir de um ficheiro .zip hospedado no armazenamento Azure Blob:

![definição de aplicativo WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Atualmente, apenas os ficheiros de pacotes .zip são suportados.

## <a name="integration-with-zip-deployment"></a>Integração com implantação zip

[A implementação em zip][Zip deployment for Azure Functions] é uma funcionalidade do Azure App `wwwroot` Service que permite implementar o seu projeto de aplicação de funções para o diretório. O projeto é embalado como um ficheiro de implantação .zip. As mesmas APIs podem ser utilizadas `d:\home\data\SitePackages` para implantar o seu pacote na pasta. Com `WEBSITE_RUN_FROM_PACKAGE` o valor `1`de definição da aplicação de `d:\home\data\SitePackages` , as APIs `d:\home\site\wwwroot`de implementação zip copiam o seu pacote para a pasta em vez de extrair os ficheiros para . Também cria `packagename.txt` o ficheiro. Após um reinício, a `wwwroot` embalagem é montada como um sistema de ficheiros apenas para leitura. Para obter mais informações sobre a implantação do zip, consulte a [implementação zip para funções Azure](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Adicionando a definição de WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Resolução de problemas

- Run From `wwwroot` Package faz apenas leitura, para que receba um erro ao escrever ficheiros para este diretório.
- Os formatos de alcatrão e gzip não são suportados.
- Esta funcionalidade não se compõe com cache local.
- Para um melhor desempenho a frio, utilize`WEBSITE_RUN_FROM_PACKAGE`a opção Zip local (=1).
- Run From Package é incompatível com`SCM_DO_BUILD_DURING_DEPLOYMENT=true`a opção de personalização de implementação ( ), o passo de construção será ignorado durante a implementação.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
