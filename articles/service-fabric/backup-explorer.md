---
title: Leia e atualize uma cópia de segurança das Coleções Fiáveis localmente
description: Utilize o Backup Explorer no Azure Service Fabric para ler e atualizar uma cópia de segurança local das Coleções Fidedignas.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: a0131960d356e4862d1379c308e240e19e76205c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048073"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Leia e atualize uma cópia de segurança de Cobranças Fiáveis utilizando o Backup Explorer

O Azure Service Fabric Backup Explorer ajuda na correção de dados se os dados forem corrompidos em Coleções Fiáveis de Tecido de Serviço. O estado atual dos dados pode ser corrompido por qualquer bug que seja introduzido numa aplicação ou por quaisquer entradas erradas feitas num cluster ao vivo.

Com a ajuda do Backup Explorer, pode fazer as seguintes tarefas:
-   Consultar os metadados para a recolha.
-   Veja o estado atual e as suas entradas na recolha do backup que está carregado.
-   Listar as transações realizadas desde o último ponto de verificação.
-   Atualize a coleção adicionando, atualizando ou eliminando entradas na coleção.
-   Faça uma nova cópia de segurança utilizando o estado atualizado.

> [!NOTE]
> O Service Fabric Backup Explorer suporta atualmente apenas a visualização e edição de Coleções Fiáveis na cópia de segurança.
>

## <a name="access-the-backup"></a>Aceda à cópia de segurança

O Service Fabric Backup Explorer pode ser consumido de qualquer uma das seguintes formas de visualização ou atualização de Coleções Fiáveis na cópia de segurança:
-   **Binário**: Utilize um pacote NuGet para visualizar e alterar coleções fiáveis.
-   **HTTP/REST**: Utilize um servidor REST baseado em HTTP para visualizar e alterar coleções fiáveis.
-   **bkpctl**: Utilize a interface de linha de comando do Explorador de Backup do Tecido de Serviço (CLI) para visualizar e alterar uma cópia de segurança de cobranças fiáveis.

O Backup Explorer tem uma biblioteca C# para utilizadores avançados. Você pode usar a biblioteca na aplicação diretamente para trabalhar com Coleções Fiáveis semelhante à forma como os clientes trabalham com o gerente do estado nos seus serviços estatais existentes. Para utilizadores básicos e em caso de utilização básica, o explorador também tem um servidor REST autónomo que expõe APIs para inspecionar as cópias de segurança. A ferramenta BKPCTL CLI funciona em cima das APIs REST e é baseada em Python. Pode utilizar a ferramenta CLI para ler e atualizar cópias de segurança e para fazer novas cópias de segurança através da linha de comando.

Para mais informações, consulte o repositório gitHub [do Service Fabric Backup](https://github.com/microsoft/service-fabric-backup-explorer) Explorer. O repositório contém informações de origem e divulgação e instruções de configuração.

Você também pode construir o repositório localmente e trabalhar em backups.
 
O NuGet for Backup Explorer (Microsoft.ServiceFabric.ReliableCollectionBackup.Parser) estará disponível no [nuget.org](https://www.nuget.org/). 

## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre [As Coleções Fiáveis em Serviços Azure Fabric serviços estatais](service-fabric-reliable-services-reliable-collections.md).
* Rever [as melhores práticas do Serviço de Revisão.](./service-fabric-best-practices-security.md)