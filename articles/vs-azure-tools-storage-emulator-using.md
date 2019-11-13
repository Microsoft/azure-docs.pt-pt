---
title: Configurando e usando o emulador de armazenamento com o Visual Studio | Microsoft Docs
description: Configurar e usar o emulador de armazenamento, um utilitário que simula os serviços de armazenamento de BLOBs, filas e tabelas disponíveis no Azure em seu computador de desenvolvimento local.
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
ms.openlocfilehash: aca4bf5017a4ee23d69016b937673443c1a0e200
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935838"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configurando e usando o emulador de armazenamento com o Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral
O ambiente de desenvolvimento do SDK do Azure inclui o emulador de armazenamento, um utilitário que simula os serviços de armazenamento de BLOBs, filas e tabelas disponíveis no Azure em seu computador de desenvolvimento local. Se você estiver criando um serviço de nuvem que emprega os serviços de armazenamento do Azure ou gravando qualquer aplicativo externo que chama os serviços de armazenamento, você pode testar seu código localmente no emulador de armazenamento. As ferramentas do Azure para Microsoft Visual Studio integram o gerenciamento do emulador de armazenamento no Visual Studio. As ferramentas do Azure inicializam o banco de dados do emulador de armazenamento no primeiro uso, inicia o serviço de emulador de armazenamento quando você executa ou depura seu código do Visual Studio e fornece acesso somente leitura aos dados do emulador de armazenamento por meio do Gerenciador de Armazenamento do Azure.

Para obter informações detalhadas sobre o emulador de armazenamento, incluindo requisitos de sistema e instruções de configuração personalizadas, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage/common/storage-use-emulator.md).

> [!NOTE]
> Há algumas diferenças na funcionalidade entre a simulação do emulador de armazenamento e os serviços de armazenamento do Azure. Consulte as [diferenças entre o emulador de armazenamento e os serviços de armazenamento do Azure](storage/common/storage-use-emulator.md) na documentação do SDK do Azure para obter informações sobre as diferenças específicas.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurando uma cadeia de conexão para o emulador de armazenamento
Para acessar o emulador de armazenamento do código dentro de uma função, você desejará configurar uma cadeia de conexão que aponte para o emulador de armazenamento e que posteriormente possa ser alterada para apontar para uma conta de armazenamento do Azure. Uma cadeia de conexão é uma definição de configuração que sua função pode ler em tempo de execução para se conectar a uma conta de armazenamento. Para obter mais informações sobre como criar cadeias de conexão, consulte [Configurar cadeias de conexão do armazenamento do Azure](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Você pode retornar uma referência à conta do emulador de armazenamento do seu código usando a propriedade **DevelopmentStorageAccount** . Essa abordagem funciona corretamente se você quiser acessar o emulador de armazenamento do seu código, mas se você planeja publicar seu aplicativo no Azure, será necessário criar uma cadeia de conexão para acessar sua conta de armazenamento do Azure e modificar seu código para usar essa conexão Cadeia de caracteres antes de publicá-la. Se você estiver alternando entre a conta do emulador de armazenamento e uma conta de armazenamento do Azure com frequência, uma cadeia de conexão simplificará esse processo.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializando e executando o emulador de armazenamento
Você pode especificar que, quando executar ou depurar seu serviço no Visual Studio, o Visual Studio iniciará automaticamente o emulador de armazenamento. No Gerenciador de Soluções, abra o menu de atalho para seu projeto **do Azure** e escolha **Propriedades**. Na guia **desenvolvimento** , na lista **iniciar o emulador de armazenamento do Azure** , escolha **verdadeiro** (se ainda não estiver definido para esse valor).

Na primeira vez que você executar ou depurar o serviço do Visual Studio, o emulador de armazenamento iniciará um processo de inicialização. Esse processo reserva portas locais para o emulador de armazenamento e cria o banco de dados do emulador de armazenamento. Depois de concluído, esse processo não precisará ser executado novamente, a menos que o banco de dados do emulador de armazenamento seja excluído.

> [!NOTE]
> A partir da versão de junho de 2012 das ferramentas do Azure, o emulador de armazenamento é executado, por padrão, no SQL Express LocalDB. Em versões anteriores das ferramentas do Azure, o emulador de armazenamento é executado em uma instância padrão do SQL Express 2005 ou 2008, que deve ser instalada antes que você possa instalar o SDK do Azure. Você também pode executar o emulador de armazenamento em uma instância nomeada do SQL Express ou em uma instância nomeada ou padrão do Microsoft SQL Server. Se você precisar configurar o emulador de armazenamento para executar em uma instância diferente da instância padrão, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage/common/storage-use-emulator.md).
> 
> 

O emulador de armazenamento fornece uma interface do usuário para exibir o status dos serviços de armazenamento local e iniciá-los, pará-los e redefini-los. Depois que o serviço do emulador de armazenamento tiver sido iniciado, você poderá exibir a interface do usuário ou iniciar ou parar o serviço clicando com o botão direito do mouse no ícone da área de notificação para o emulador de Microsoft Azure na barra de tarefas do Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Exibindo dados do emulador de armazenamento no Gerenciador de Servidores
O nó armazenamento do Azure no Gerenciador de Servidores permite que você exiba dados e altere as configurações dos dados de BLOB e tabela em suas contas de armazenamento, incluindo o emulador de armazenamento. Consulte [gerenciar recursos de armazenamento de BLOBs do Azure com Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) para obter mais informações.

