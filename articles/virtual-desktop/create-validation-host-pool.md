---
title: Criar um conjunto de anfitrião de pré-visualização de área de Trabalho Virtual do Windows para validar as atualizações de serviço - Azure
description: Como criar um agrupamento de anfitrião de validação para monitorizar as atualizações de serviço antes de implementar atualizações para a produção.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b654c177accb92d5b9a861f14874e795f4525a43
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761213"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Criar um conjunto de anfitrião para validar as atualizações de serviço

Conjuntos de anfitrião são uma coleção de um ou mais máquinas virtuais idênticas em ambientes de inquilino do Windows Virtual Desktop Preview. Antes de implementar conjuntos de anfitrião para o seu ambiente de produção, é altamente recomendável que cria um conjunto de anfitrião de validação. As atualizações são aplicadas pela primeira vez para conjuntos de anfitrião de validação, permitindo-lhe monitorizar atualizações de serviço antes de distribuí-las ao seu ambiente de produção. Sem um conjunto de anfitrião de validação, não pode descobrir alterações que apresentam erros, que podem resultar em tempo de inatividade para os utilizadores no seu ambiente de produção.

Para garantir que seu trabalho de aplicações com as atualizações mais recentes, o conjunto de anfitrião de validação deve ser o mais semelhante aos agrupamentos de host no seu ambiente de produção quanto possível. Os utilizadores devem ligar com tanta freqüência ao agrupamento de anfitrião de validação, tal como ao agrupamento de host de produção. Se tem testes automatizados no seu conjunto de host, deve incluir o teste automatizado no pool de anfitrião de validação.

Pode depurar problemas no agrupamento de anfitrião de validação com o serviço de diagnóstico ou o [Windows área de Trabalho Virtual artigos de resolução de problemas](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Recomendamos que deixe o agrupamento de anfitrião de validação no local para testar todas as atualizações futuras.

Antes de começar, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), se ainda não o fez.

## <a name="create-your-host-pool"></a>Criar o conjunto de anfitrião

Pode criar um conjunto de anfitrião ao seguir as instruções em qualquer um dos seguintes artigos:
- [Tutorial: Criar um conjunto de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitrião com um modelo Azure Resource Manager](create-host-pools-arm-template.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definir o conjunto de anfitrião que um conjunto de anfitrião de validação

Execute os seguintes cmdlets do PowerShell para definir o novo conjunto de anfitrião que um conjunto de anfitrião de validação. Substitua os valores são aspas pelos valores relevantes à sua sessão:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Execute o seguinte cmdlet do PowerShell para confirmar que a propriedade de validação foi definida. Substitua os valores são aspas pelos valores relevantes à sua sessão.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Os resultados do cmdlet devem ter um aspeto semelhantes a esta saída:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Agenda de atualização

Em pré-visualização, as atualizações de serviço ocorrem em aproximadamente uma cadência mensal. Se existirem problemas principais, serão fornecidas atualizações críticas a uma cadência mais freqüente.

## <a name="next-steps"></a>Passos Seguintes

Agora que criou um conjunto de anfitrião de validação, opcionalmente, pode preencher o conjunto de anfitrião de validação com RemoteApps. Para saber mais sobre como gerir aplicações na área de Trabalho Virtual do Windows, veja o tutorial de grupos de aplicações de gerir.

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](./manage-app-groups.md)
