---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6882c46ec0e4925c42de86c87225e9509c84df84
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159842"
---
## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento
Em seguida, configure o ambiente de desenvolvimento no Visual Studio, para estar pronto para experimentar os exemplos de código deste guia.

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicação de consola do Windows
No Visual Studio, crie uma nova aplicação de consola do Windows. Os passos seguintes mostram como criar uma aplicação de consola no Visual Studio 2017. Os passos são semelhantes aos de outras versões do Visual Studio.

1. Selecione **Ficheiro** > **Novo** > **Projeto**.
2. Selecione **Instalado** > **Modelos** > **Visual C#** > **Ambiente de Trabalho Clássico do Windows**.
3. Selecione **Aplicação da Consola (.NET Framework)**.
4. No campo **Nome**, introduza um nome para a sua aplicação.
5. Selecione **OK**.

![Captura de ecrã da caixa de diálogo Novo Projeto no Visual Studio](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Todos os exemplos de código deste tutorial podem ser adicionados ao método `Main()` do ficheiro `Program.cs` da aplicação da consola.

Pode utilizar a Biblioteca de Clientes de Armazenamento do Azure em qualquer tipo de aplicação .NET, incluindo um serviço cloud do Azure ou aplicação Web, e aplicações de ambiente de trabalho ou móveis. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

### <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários
Há dois pacotes que tem de fazer referência no seu projeto para concluir este tutorial:

* [Biblioteca de cliente de armazenamento do Microsoft Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): Este pacote fornece acesso programático a recursos de dados na sua conta de armazenamento.
* [Biblioteca do Gestor de configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Este pacote fornece uma classe para analisar uma cadeia de ligação num ficheiro de configuração, independentemente de onde a sua aplicação está em execução.

Pode utilizar o NuGet para obter ambos os pacotes. Siga estes passos.

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.
2. Procure online “WindowsAzure.Storage” e selecione **Instalar** para instalar a Biblioteca de Clientes de Armazenamento e as respetivas dependências.
3. Procure online “WindowsAzure.ConfigurationManager” e selecione **Instalar** para instalar o Gestor de Configuração do Azure.

> [!NOTE]
> O pacote Biblioteca de Clientes de Armazenamento também está incluído no [Azure SDK para .NET](https://azure.microsoft.com/downloads/). Recomendamos, no entanto, que instale também a Biblioteca de Clientes de Armazenamento a partir do NuGet para garantir que terá sempre a versão mais recente da biblioteca de clientes.
> 
> As dependências ODataLib na Biblioteca de Clientes de Armazenamento para .NET são resolvidas através de pacotes ODataLib disponíveis no NuGet e não através dos Serviços de Dados WCF. As bibliotecas ODataLib podem ser transferidas diretamente ou referenciadas pelo seu projeto de código através do NuGet. Os pacotes ODataLib específicos utilizados da Biblioteca de Clientes de Armazenamento são [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) e [Spatial](http://nuget.org/packages/System.Spatial/). Embora estas bibliotecas sejam utilizadas pelas classes de Armazenamento de tabelas do Azure, são dependências necessárias para a programação com a Biblioteca de Clientes de Armazenamento.
> 
> 

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino
Tem duas opções de ambiente para executar os exemplos neste guia:

* Pode executar o código numa conta de armazenamento do Azure na nuvem. 
* Pode executar o código no emulador de armazenamento do Azure. O emulador de armazenamento é um ambiente local que emula uma conta de armazenamento do Azure na nuvem. O emulador é uma opção gratuita para testar e depurar o seu código enquanto a aplicação está em desenvolvimento. O emulador utiliza uma conta e chave bem conhecidas. Para obter mais informações, veja [Utilizar o emulador de armazenamento do Azure para programação e teste](../articles/storage/common/storage-use-emulator.md).

Se estiver a filtrar uma conta de armazenamento na cloud, copie a chave de acesso primária para a sua conta de armazenamento a partir do portal do Azure. Para obter mais informações, veja [Chaves de acesso](../articles/storage/common/storage-account-manage.md#access-keys).

> [!NOTE]
> Pode filtrar o emulador de armazenamento para evitar incorrer em custos associados ao Storage do Azure. No entanto, se optar por filtrar uma conta de armazenamento do Azure na nuvem, os custos para efetuar este tutorial serão negligenciável.
> 
> 

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
A Biblioteca de Clientes de Armazenamento do Azure para .NET suporta a utilização de uma cadeia de ligação de armazenamento para configurar pontos finais e credenciais para aceder aos serviços de armazenamento. A melhor forma para manter a cadeia de ligação de armazenamento é num ficheiro de configuração. 

Para obter mais informações sobre as cadeias de ligação, veja [Configurar uma cadeia de ligação para o Armazenamento do Azure](../articles/storage/common/storage-configure-connection-string.md).

> [!NOTE]
> A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Tenha sempre o cuidado de proteger a chave da conta de armazenamento. Evite distribui-la a outros utilizadores, pré-programá-la ou guardá-la num ficheiro de texto simples que seja acessível a outras pessoas. Regenere a sua chave através do portal do Azure se considerar que poderá ter sido comprometida.
> 
> 

Para configurar a cadeia de ligação, abra o ficheiro `app.config` a partir do Explorador de Soluções no Visual Studio. Adicione o conteúdo do elemento `<appSettings>` mostrado abaixo. Substitua `account-name` pelo nome da sua conta de armazenamento e `account-key` pela chave de acesso da conta.

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Por exemplo, a definição de configuração é semelhante a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Para filtrar o emulador de armazenamento, pode utilizar um atalho que mapeia para o nome de conta e chave bem conhecidas. Nesse caso, a definição da cadeia de ligação é:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

