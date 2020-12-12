---
title: Carregar métricas para o Monitor Azure
description: Upload Azure Arc habilitado métricas de serviços de dados para Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f319f912520a69a0c68f89a3d4178f63cc45ca1f
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356553"
---
# <a name="upload-metrics-to-azure-monitor"></a>Carregar métricas para o Monitor Azure

Periodicamente, pode exportar métricas de monitorização e depois enviá-las para Azure. A exportação e upload de dados também cria e atualiza o controlador de dados, o exemplo gerido pelo SQL e os recursos do grupo de servidores de hiperescala PostgreSQL em Azure.

> [!NOTE] 
> Durante o período de pré-visualização, não há qualquer custo para a utilização de serviços de dados ativados pelo Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, certifique-se de ter criado o principal de serviço necessário e atribuiu-o a um papel apropriado. Para obter mais detalhes, veja:
* [Criar o principal de serviço.](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
* [Atribuir funções ao diretor de serviço](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Carregar métricas

Com os serviços de dados do Azure Arc, pode enviar opcionalmente as suas métricas para o Azure Monitor para que possa agregar e analisar métricas, elevar alertas, enviar notificações ou desencadear ações automatizadas. 

O envio dos seus dados para o Azure Monitor também permite armazenar dados de métricas fora do site e em grande escala, permitindo o armazenamento a longo prazo dos dados para análises avançadas.

Se tiver vários sites que tenham serviços de dados do Azure Arc, pode utilizar o Azure Monitor como localização central para recolher todos os seus registos e métricas em todos os seus sites.

## <a name="set-final-environment-variables-and-confirm"></a>Definir variáveis de ambiente final e confirmar

Definir o URL de autoridade SPN numa variável ambiental:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Verifique se todas as variáveis ambientais necessárias são definidas se quiser:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Carregar métricas para o Monitor Azure

Para carregar métricas para o seu arco Azure habilitado a sql gerido instâncias e Azure Arc ativado grupos de servidores de hiperescala postgreSQL funcionam, os seguintes comandos CLI:

1. Faça login no controlador de dados com `azdata` .
 
1. Exportar todas as métricas para o ficheiro especificado:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Carregar métricas para o monitor Azure:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Aguarde pelo menos 30 minutos após a criação de casos de dados ativados pelo Arco Azure para o primeiro upload.
   >
   >Certifique-se de que `upload` as métricas imediatamente a `export` seguir, uma vez que o Azure Monitor só aceita métricas durante os últimos 30 minutos. [Saiba mais](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting).


Se vir algum erro que indique "Falha na obter métricas" durante a exportação, verifique se a recolha de dados está definida `true` para executar o seguinte comando:

```console
azdata arc dc config show
```

Veja em "secção de segurança"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Verifique se as `allowNodeMetricsCollection` propriedades e `allowPodMetricsCollection` propriedades estão definidas para `true` .

## <a name="view-the-metrics-in-the-portal"></a>Ver as métricas no Portal

Uma vez que as suas métricas são carregadas, você pode vê-las a partir do portal Azure.
> [!NOTE]
> Por favor, note que pode levar alguns minutos para que os dados carregados sejam processados antes de poder ver as métricas no portal.


Para ver as suas métricas no portal, utilize este link para abrir o portal: Em seguida, procure a <https://portal.azure.com> sua caixa de dados por exemplo na barra de pesquisa:

Pode ver a utilização do CPU na página 'Vista Geral' ou se quiser métricas mais detalhadas, pode clicar em métricas a partir do painel de navegação à esquerda

Escolha o servidor sql como o espaço de nome métrico:

Selecione a métrica que pretende visualizar (também pode selecionar vários):

Altere a frequência para durar 30 minutos:

> [!NOTE]
> Só é possível carregar métricas nos últimos 30 minutos. O Azure Monitor rejeita métricas com mais de 30 minutos.

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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Orientações gerais sobre exportação e upload de utilização, métricas

Criar, ler, atualizar e eliminar (CRUD) operações em Azure Arc os serviços de dados habilitados são registados para efeitos de faturação e monitorização. Existem serviços de fundo que monitorizam estas operações crud e calculam o consumo adequadamente. O cálculo real da utilização ou consumo ocorre numa base programada e é feito em segundo plano. 

Durante a pré-visualização, este processo acontece todas as noites. A orientação geral é fazer o upload do uso apenas uma vez por dia. Quando as informações de utilização são exportadas e carregadas várias vezes no mesmo período de 24 horas, apenas o inventário de recursos é atualizado no portal Azure, mas não no uso do recurso.

Para carregar métricas, o monitor Azure só aceita os últimos 30 minutos de dados[(Saiba mais).](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting) A orientação para o upload das métricas é carregar as métricas imediatamente após a criação do ficheiro de exportação para que possa ver todo o conjunto de dados no portal Azure. Por exemplo, se exportasse as métricas às 14:00 e executasse o comando de upload às 14:50. Uma vez que o Azure Monitor só aceita dados durante os últimos 30 minutos, poderá não ver quaisquer dados no portal. 

## <a name="next-steps"></a>Passos seguintes

[Faça upload de registos para O Azure Monitor](upload-logs.md)

[Faça upload de dados de utilização, métricas e registos para o Azure Monitor](upload-usage-data.md)

[Faça upload de dados de faturação para a Azure e veja-os no portal Azure](view-billing-data-in-azure.md)

[Ver recurso do controlador de dados Azure Arc no portal Azure](view-data-controller-in-azure-portal.md)
