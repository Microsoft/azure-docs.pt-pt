---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689571"
---
Se não conseguir utilizar pacotes de extensões, pode utilizar as Ferramentas Principais do Azure Functions localmente para instalar os pacotes de extensão específicos exigidos pelo seu projeto.

> [!IMPORTANT]
> Não é possível instalar explicitamente extensões numa aplicação de função que está a usar pacotes de extensão. Retire a `extensionBundle` secçãohost.js* antes* de instalar explicitamente as extensões.

Os seguintes itens descrevem algumas razões pelas quais poderá necessitar de instalar as extensões manualmente:

* Você precisa ter acesso a uma versão específica de uma extensão não disponível em um pacote.
* Você precisa ter acesso a uma extensão personalizada não disponível em um pacote.
* Você precisa ter acesso a uma combinação específica de extensões não disponíveis em um único pacote.

> [!NOTE]
> Para instalar manualmente extensões utilizando ferramentas principais, tem de ter o [.NET Core 2.x SDK](https://dotnet.microsoft.com/download) instalado. O .NET Core SDK é utilizado pelas Ferramentas Principais do Azure Functions para instalar extensões a partir de NuGet. Não precisa de saber .NET para utilizar extensões de Funções Azure.

Quando instala explicitamente extensões, um ficheiro de projeto .NET denominado extensions.csproj é adicionado à raiz do seu projeto. Este ficheiro define o conjunto de pacotes NuGet exigidos pelas suas funções. Enquanto pode trabalhar com as referências de [pacotes NuGet](/nuget/consume-packages/package-references-in-project-files) neste ficheiro, o Core Tools permite instalar extensões sem ter de editar manualmente o ficheiro.

Existem várias formas de usar as Ferramentas Core para instalar as extensões necessárias no seu projeto local. 

#### <a name="install-all-extensions"></a>Instalar todas as extensões 

Utilize o seguinte comando para adicionar automaticamente todos os pacotes de extensão utilizados pelas ligações no seu projeto local:

```dotnetcli
func extensions install
```
O comando lê o *function.jsem* ficheiro para ver quais os pacotes de que necessita, instala-os e reconstrói o projeto de extensões (extensões.csproj). Adiciona quaisquer novas ligações na versão atual, mas não atualiza as ligações existentes. Utilize a `--force` opção para atualizar as ligações existentes para a versão mais recente ao instalar as novas.

Se a sua aplicação de função utilizar ligações que as Ferramentas Core não reconhecem, tem de instalar manualmente a extensão específica.

#### <a name="install-a-specific-extension"></a>Instale uma extensão específica

Utilize o seguinte comando para instalar um pacote de extensão específico numa versão específica, neste caso a extensão de Armazenamento:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
