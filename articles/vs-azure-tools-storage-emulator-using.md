---
title: Configurar e utilizar o emulador de armazenamento com o Visual Studio | Documentos da Microsoft
description: Configurar e utilizar o emulador de armazenamento com o Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 39e2071a62d6a1f6ee050f862856815048e50430
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128301"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configurar e utilizar o emulador de armazenamento com o Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral
O ambiente de desenvolvimento do SDK do Azure inclui o emulador de armazenamento, um utilitário que simula a serviços de armazenamento Blob, filas e tabelas disponíveis no Azure no seu computador de desenvolvimento local. Se estiver criar um serviço em nuvem que utiliza os serviços de armazenamento do Azure ou programar qualquer aplicativo externo que chama os serviços de armazenamento, pode testar seu código localmente relativamente o emulador de armazenamento. As ferramentas do Azure para Microsoft Visual Studio integrar a gestão de emulador de armazenamento para o Visual Studio. As ferramentas do Azure inicializar a base de dados de emulador de armazenamento na primeira utilização, inicia o serviço de emulador de armazenamento, quando executa ou depura o código do Visual Studio e fornece acesso só de leitura para os dados de emulador de armazenamento através do Explorador de armazenamento do Azure.

Para obter informações detalhadas sobre o emulador de armazenamento, incluindo requisitos de sistema e obter instruções de configuração personalizada, consulte [utilizar o emulador de armazenamento do Azure para programação e teste](storage/common/storage-use-emulator.md).

> [!NOTE]
> Existem algumas diferenças de funcionalidade entre a simulação de emulador de armazenamento e os serviços de armazenamento do Azure. Ver [diferenças entre o armazenamento Emulator e serviços de armazenamento do Azure](storage/common/storage-use-emulator.md) na documentação do SDK do Azure para obter informações sobre as diferenças específicas.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurar uma cadeia de ligação para o emulador de armazenamento
Para acessar o emulador de armazenamento do código dentro de uma função, desejará configurar uma cadeia de ligação que aponta para o emulador de armazenamento e que posteriormente pode ser alterado para apontar para uma conta de armazenamento do Azure. Uma cadeia de ligação é uma definição de configuração que sua função pode ler em tempo de execução para ligar a uma conta de armazenamento. Para obter mais informações sobre como criar cadeias de ligação, veja [cadeias de ligação de configurar o armazenamento do Azure](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Pode retornar uma referência à conta de emulador de armazenamento a partir do código ao utilizar o **DevelopmentStorageAccount** propriedade. Essa abordagem funciona corretamente se desejar acessar o emulador de armazenamento a partir do código, mas se planeja publicar a aplicação no Azure, terá de criar uma cadeia de ligação para aceder à sua conta de armazenamento do Azure e modificar o código para usar essa ligação cadeia de caracteres antes de o publicar. Se estiver oscila frequentemente entre a conta de emulador de armazenamento e uma conta de armazenamento do Azure, uma cadeia de ligação irá simplificar este processo.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>A inicializar e executar o emulador de armazenamento
Pode especificar que quando executa ou depura o seu serviço no Visual Studio, o Visual Studio automaticamente inicia o emulador de armazenamento. No Explorador de soluções, abra o menu de atalho para sua **Azure** do projeto e escolha **propriedades**. Sobre o **desenvolvimento** separador a **iniciar o emulador de armazenamento do Azure** lista, escolha **verdadeiro** (se já não está definido como esse valor).

Na primeira vez que executa ou depura o seu serviço a partir do Visual Studio, o emulador de armazenamento inicia um processo de inicialização. Este processo reserva portas locais para o emulador do storage e cria a base de dados do emulador de armazenamento. Depois de concluído, este processo não precisa de executar novamente se a base de dados do emulador de armazenamento for eliminado.

> [!NOTE]
> A partir da versão de Junho de 2012 das ferramentas do Azure, o emulador de armazenamento é executado, por predefinição, no SQL Express LocalDB. Nas versões anteriores das ferramentas do Azure, o emulador de armazenamento é executado em relação a uma instância predefinida do SQL Express 2005 ou 2008, que é necessário instalar antes de pode instalar o SDK do Azure. Também pode executar o emulador de armazenamento em relação a uma instância nomeada do SQL Express ou uma determinada ou uma instância predefinida do Microsoft SQL Server. Se precisar de configurar o emulador de armazenamento para executar uma instância que não seja a instância predefinida, consulte [utilizar o emulador de armazenamento do Azure para programação e teste](storage/common/storage-use-emulator.md).
> 
> 

O emulador do storage fornece uma interface de utilizador para ver o estado dos serviços de armazenamento local e para iniciar, parar e repô-los. Assim que tiver sido iniciado o serviço de emulador de armazenamento, pode apresentar a interface do usuário de modo a iniciar ou parar o serviço clicando no ícone de área de notificação para o emulador do Microsoft Azure na barra de tarefas do Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visualizar os dados de emulador de armazenamento no Explorador de servidores
O nó de armazenamento do Azure no Explorador de servidores permite-lhe ver os dados e alterar as definições para os dados de blob e tabela nas suas contas de armazenamento, incluindo o emulador de armazenamento. Ver [recursos de gerir o armazenamento de Blobs do Azure com o Explorador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) para obter mais informações.

