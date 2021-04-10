---
title: Executar as suas Funções Azure a partir de um pacote
description: Faça com que o tempo de execução das Funções Azure execute as suas funções montando um ficheiro pacote de implementação que contenha os ficheiros de projetos de aplicações de função.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: aad6991d0ddd5c439d03e41adec63837a21db87b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581597"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Executar as suas Funções Azure a partir de um ficheiro de pacote

No Azure, pode executar as suas funções diretamente a partir de um ficheiro pacote de implementação na sua aplicação de função. A outra opção é implementar os seus ficheiros no `d:\home\site\wwwroot` diretório da sua aplicação de funções.

Este artigo descreve os benefícios de executar as suas funções a partir de um pacote. Também mostra como ativar esta funcionalidade na sua aplicação de função.

## <a name="benefits-of-running-from-a-package-file"></a>Benefícios de correr a partir de um ficheiro de pacote
  
Existem vários benefícios para correr a partir de um ficheiro de pacote:

+ Reduz o risco de problemas de bloqueio de cópias de ficheiros.
+ Pode ser implantado numa aplicação de produção (com reinício).
+ Pode ter a certeza dos ficheiros que estão a ser distribuídos na sua aplicação.
+ Melhora o desempenho das implementações do [Azure Resource Manager](functions-infrastructure-as-code.md).
+ Pode reduzir os tempos de arranque a frio, particularmente para funções JavaScript com grandes árvores de pacote npm.

Para mais informações, consulte [este anúncio.](https://github.com/Azure/app-service-announcements/issues/84)

## <a name="enabling-functions-to-run-from-a-package"></a>Habilitando funções a funcionar a partir de um pacote

Para ativar a aplicação de função a partir de um pacote, basta adicionar uma `WEBSITE_RUN_FROM_PACKAGE` definição às definições da aplicação da sua função. A `WEBSITE_RUN_FROM_PACKAGE` definição pode ter um dos seguintes valores:

| Valor  | Descrição  |
|---------|---------|
| **`1`**  | Recomendado para aplicações de função em execução no Windows. Executar a partir de um ficheiro de pacote na `d:\home\data\SitePackages` pasta da sua aplicação de função. Se não [estiver a ser implantada com fecho](#integration-with-zip-deployment)de correr, esta opção requer que a pasta também tenha um ficheiro nomeado `packagename.txt` . Este ficheiro contém apenas o nome do ficheiro do pacote na pasta, sem qualquer espaço em branco. |
|**`<URL>`**  | Localização de um ficheiro de pacote específico que pretende executar. Ao utilizar o armazenamento Blob, deve utilizar um recipiente privado com uma [Assinatura de Acesso Partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para permitir que as Funções tenham acesso ao pacote. Pode utilizar o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para enviar ficheiros de pacotes para a sua conta de armazenamento Blob. Quando especificar um URL, também deve [sincronizar os gatilhos](functions-deployment-technologies.md#trigger-syncing) depois de publicar um pacote atualizado. |

> [!CAUTION]
> Ao executar uma aplicação de função no Windows, a opção de URL externa produz um desempenho de arranque a frio pior. Ao implementar a sua aplicação de função para o Windows, deverá definir `WEBSITE_RUN_FROM_PACKAGE` e publicar com a `1` implementação zip.

O seguinte mostra uma aplicação de função configurada para ser executada a partir de um ficheiro .zip alojado no armazenamento Azure Blob:

![WEBSITE_RUN_FROM_ZIP configuração de aplicativo](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Atualmente, apenas .zip ficheiros de pacotes são suportados.

## <a name="integration-with-zip-deployment"></a>Integração com implantação zip

[A implementação][Zip deployment for Azure Functions] zip é uma funcionalidade do Azure App Service que permite implementar o seu projeto de aplicação de função para o `wwwroot` diretório. O projeto é embalado como um ficheiro de implantação .zip. As mesmas APIs podem ser utilizadas para implantar a sua embalagem na `d:\home\data\SitePackages` pasta. Com o `WEBSITE_RUN_FROM_PACKAGE` valor de definição de aplicação `1` de , as APIs de implementação zip copiam o seu pacote para a `d:\home\data\SitePackages` pasta em vez de extrair os ficheiros para `d:\home\site\wwwroot` . Também cria o `packagename.txt` ficheiro. Após um reinício, a embalagem é montada `wwwroot` como um sistema de ficheiros apenas de leitura. Para obter mais informações sobre a colocação de zíper, consulte [a implementação zip para funções Azure](deployment-zip-push.md).

> [!NOTE]
> Quando ocorre uma implementação, é ativada uma reiniciada aplicação de função. Antes de recomeçar, todas as execuções de funções existentes são permitidas para completar ou sair do tempo. Para saber mais, consulte [os comportamentos de Implementação.](functions-deployment-technologies.md#deployment-behaviors)

## <a name="adding-the-website_run_from_package-setting"></a>Adicionar a definição de WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Resolução de problemas

- O Run From Package faz `wwwroot` apenas leitura, para que receba um erro ao escrever ficheiros para este diretório.
- Os formatos de alcatrão e gzip não são suportados.
- O ficheiro ZIP pode ser no máximo 1GB.
- Esta função não compõe com cache local.
- Para melhorar o desempenho do arranque a frio, utilize a opção Zip local `WEBSITE_RUN_FROM_PACKAGE` (=1).
- O Run From Package é incompatível com a opção de personalização de implementação `SCM_DO_BUILD_DURING_DEPLOYMENT=true` (), o passo de construção será ignorado durante a implementação.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
