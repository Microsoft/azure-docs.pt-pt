---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031133"
---
Se não conseguir utilizar pacotes de extensões, pode utilizar as Ferramentas Principais do Azure Functions localmente para instalar os pacotes de extensão específicos exigidos pelo seu projeto. 

> [!NOTE]
> Para instalar manualmente extensões utilizando ferramentas principais, tem de ter o .NET Core 2.x SDK instalado.

Quando instala explicitamente extensões, um ficheiro de projeto .NET denominado extensions.csproj é adicionado à raiz do seu projeto. Este ficheiro define o conjunto de pacotes NuGet exigidos pelas suas funções. Enquanto pode trabalhar com as referências de [pacotes NuGet](/nuget/consume-packages/package-references-in-project-files) neste ficheiro, o Core Tools permite instalar extensões sem ter de editar manualmente o ficheiro.

Existem várias formas de usar as Ferramentas Core para instalar as extensões necessárias no seu projeto local. 

#### <a name="install-all-extensions"></a>Instalar todas as extensões 

Utilize o seguinte comando para adicionar automaticamente todos os pacotes de extensão utilizados pelas ligações no seu projeto local:

```dotnetcli
func extensions install
```
O comando lê o *function.jsem* ficheiro para ver quais os pacotes de que necessita, instala-os e reconstrói o projeto de extensões (extensões.csproj). Adiciona quaisquer novas ligações na versão atual, mas não atualiza as ligações existentes. Utilize a `--force` opção para atualizar as ligações existentes para a versão mais recente ao instalar as novas.

#### <a name="install-a-specific-extension"></a>Instale uma extensão específica

Utilize o seguinte comando para instalar um pacote de extensão específico numa versão específica, neste caso a extensão de Armazenamento:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```