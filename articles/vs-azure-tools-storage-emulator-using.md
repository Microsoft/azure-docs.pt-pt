---
title: Configurar e utilizar o Emulador de Armazenamento com Estúdio Visual [ Microsoft Docs
description: Configurar e utilizar o emulador de armazenamento, um utilitário que simula os serviços de armazenamento Blob, Queue e Table disponíveis em Azure na sua máquina de desenvolvimento local.
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: a6f853924416cce2440ca15767044029b20e651f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75450730"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configuração e Utilização do Emulador de Armazenamento com Estúdio Visual

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral

O ambiente de desenvolvimento do Azure SDK inclui o emulador de armazenamento, um utilitário que simula os serviços de armazenamento blob, fila e mesa disponíveis em Azure na sua máquina de desenvolvimento local. Se estiver a construir um serviço na nuvem que utilize os serviços de armazenamento Azure, ou a escrever qualquer aplicação externa que ligue para os serviços de armazenamento, pode testar o seu código localmente contra o emulador de armazenamento. As Ferramentas Azure para o Microsoft Visual Studio integram a gestão do emulador de armazenamento no Estúdio Visual. As Ferramentas Azure inicializam a base de dados do emulador de armazenamento na primeira utilização, inicia o serviço de emulador de armazenamento quando executa ou depura o seu código do Visual Studio, e fornece acesso apenas a leitura sem dados de emuladores de armazenamento através do Azure Storage Explorer.

Para obter informações detalhadas sobre o emulador de armazenamento, incluindo requisitos do sistema e instruções de configuração personalizadas, consulte [Utilize o Emulador](storage/common/storage-use-emulator.md)de Armazenamento Azure para Desenvolvimento e Teste .

> [!NOTE]
> Existem algumas diferenças na funcionalidade entre a simulação do emulador de armazenamento e os serviços de armazenamento Azure. Ver [Diferenças Entre o Emmulador de Armazenamento e os Serviços](storage/common/storage-use-emulator.md) de Armazenamento Azure na documentação Azure SDK para obter informações sobre as diferenças específicas.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurar uma cadeia de ligação para o emulador de armazenamento

Para aceder ao emulador de armazenamento a partir de código dentro de uma função, você vai querer configurar uma cadeia de ligação que aponta para o emulador de armazenamento e que pode ser posteriormente alterada para apontar para uma conta de armazenamento Azure. Uma cadeia de ligação é uma definição de configuração que o seu papel pode ler no tempo de execução para se ligar a uma conta de armazenamento. Para obter mais informações sobre como criar cordas de ligação, consulte as cordas de ligação de [Configuração Azure .](/azure/storage/common/storage-configure-connection-string)

> [!NOTE]
> Pode devolver uma referência à conta emulador de armazenamento do seu código utilizando a propriedade **DevelopmentStorageAccount.** Esta abordagem funciona corretamente se pretender aceder ao emulador de armazenamento a partir do seu código, mas se planeia publicar a sua aplicação no Azure, terá de criar uma cadeia de ligação para aceder à sua conta de armazenamento Azure e modificar o seu código para utilizar essa cadeia de ligação antes de publicá-la. Se estiver a alternar frequentemente entre a conta de emulador de armazenamento e uma conta de armazenamento Azure, uma cadeia de ligação simplificará este processo.

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializar e executar o emulador de armazenamento

Pode especificar que quando executa ou depura o seu serviço no Visual Studio, o Visual Studio lança automaticamente o emulador de armazenamento. No Solution Explorer, abra o menu de atalho para o seu projeto **Azure** e escolha **Propriedades**. No separador **Desenvolvimento,** na lista de **Emuladores de Armazenamento Start Azure,** escolha **True** (se ainda não está definido para esse valor).  Alguns tipos de projetos não têm o separador **Desenvolvimento.** Se for esse o caso, pode ativar ou desativar `StartDevelopmentStorage` o arranque do emulador de armazenamento, definindo o elemento no ficheiro do projeto. Desative-o ao **True** para o ativar, ou **falso** para desativá-lo.  Por exemplo, num projeto de Funções Azure, abra o ficheiro do projeto para edição e modifique o código XML da seguinte forma:

```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
    <StartDevelopmentStorage>True</StartDevelopmentStorage>
  </PropertyGroup>
```

A primeira vez que executa ou depura o seu serviço do Visual Studio, o emulador de armazenamento lança um processo de inicialização. Este processo reserva portas locais para o emulador de armazenamento e cria a base de dados do emulador de armazenamento. Uma vez concluído, este processo não necessita de ser executado novamente a menos que a base de dados do emulador de armazenamento seja eliminada.

> [!NOTE]
> A partir do lançamento de junho de 2012 das Ferramentas Azure, o emulador de armazenamento funciona, por defeito, no SQL Express LocalDB. Em lançamentos anteriores das Ferramentas Azure, o emulador de armazenamento vai contra uma instância predefinida do SQL Express 2005 ou 2008, que deve instalar antes de poder instalar o Azure SDK. Também pode executar o emulador de armazenamento contra uma instância nomeada do SQL Express ou uma instância nomeada ou padrão do Microsoft SQL Server. Se precisar de configurar o emulador de armazenamento para correr contra uma instância diferente da instância predefinida, consulte [Utilize o Emulador](storage/common/storage-use-emulator.md)de Armazenamento Azure para Desenvolvimento e Teste .

O emulador de armazenamento fornece uma interface de utilizador para visualizar o estado dos serviços de armazenamento locais e para iniciar, parar e reset-los. Uma vez iniciado o serviço de emulador de armazenamento, pode exibir a interface do utilizador ou iniciar ou parar o serviço clicando corretamente no ícone da área de notificação do Emulador Microsoft Azure na barra de tarefas do Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visualização de dados do emulador de armazenamento no Server Explorer

O nó de Armazenamento Azure no Server Explorer permite-lhe visualizar dados e alterar as definições para os dados de blob e tabela nas suas contas de armazenamento, incluindo o emulador de armazenamento. Consulte [a Manage Azure Blob Storage resources with Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) para obter mais informações.

