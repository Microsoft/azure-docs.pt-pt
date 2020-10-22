---
title: Faça upload de dados de utilização para o Azure Monitor
description: O upload do uso Azure Arc permitiu dados de serviços de dados para o Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378123"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Faça upload de dados de utilização para o Azure Monitor

Periodicamente, pode exportar informações de utilização. A exportação e upload desta informação cria e atualiza o controlador de dados, a instância gerida pelo SQL e os recursos do grupo de servidores de hiperescala PostgreSQL em Azure.

> [!NOTE] 
> Durante o período de pré-visualização, não há qualquer custo para a utilização de serviços de dados ativados pelo Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Aguarde pelo menos 24 horas após a criação do controlador de dados Azure Arc antes de carregar os dados de utilização.

## <a name="create-service-principal-and-assign-roles"></a>Criar funções principais de serviço e atribuir funções

Antes de prosseguir, certifique-se de ter criado o principal de serviço necessário e atribuiu-o a um papel apropriado. Para obter mais detalhes, veja:
* [Criar o principal de serviço.](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
* [Atribuir funções ao diretor de serviço](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Carregar dados de utilização

As informações de utilização, tais como o inventário e a utilização de recursos, podem ser enviadas para a Azure da seguinte forma:

1. Faça login no controlador de dados. Introduza os valores no momento. 

   ```console
   azdata login
   ```

1. Exportar os dados de utilização utilizando `azdata arc dc export` o comando, da seguinte forma:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Este comando cria um `usage.json` ficheiro com todos os recursos de dados ativados pelo Arco Azure, tais como instâncias geridas pelo SQL e instâncias de hiperescala postgresQL, etc. que são criadas no controlador de dados.

2. Faça o upload dos dados de utilização usando ```azdata upload``` o comando

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Automatização de uploads (opcional)

Se quiser carregar métricas e registos numa base programada, pode criar um script e executá-lo num temporizador a cada poucos minutos. Abaixo está um exemplo de automatização dos uploads usando um script linux shell.

No seu editor de texto/código favorito, adicione o seguinte script ao ficheiro e guarde como um ficheiro executável de scripts como .sh (Linux/Mac) ou .cmd, .bat, .ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Tornar o ficheiro de script executável

```console
chmod +x myuploadscript.sh
```

Execute o guião a cada 20 minutos:

```console
watch -n 1200 ./myuploadscript.sh
```

Você também pode usar um programador de trabalho como cron ou Windows Task Scheduler ou um orquestrador como Ansible, Puppet ou Chef.

## <a name="next-steps"></a>Passos seguintes

[Carregar métricas e registos para O Azure Monitor](upload-metrics.md)

[Faça upload de registos para O Azure Monitor](upload-logs.md)

[Faça upload de dados de faturação para a Azure e veja-os no portal Azure](view-billing-data-in-azure.md)

[Ver recurso do controlador de dados Azure Arc no portal Azure](view-data-controller-in-azure-portal.md)
