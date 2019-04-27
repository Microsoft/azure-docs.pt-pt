---
title: Tópicos de atualização de aplicativos avançados | Documentos da Microsoft
description: Este artigo aborda alguns tópicos avançados relativas à atualização de uma aplicação do Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3cdddac74552b56dfe3567adf30f1a05b6eb8e24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616539"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização da aplicação de Service Fabric: Tópicos avançados
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Adicionar ou remover tipos de serviço durante uma atualização da aplicação
Se um novo tipo de serviço for adicionado a uma aplicação publicada como parte de uma atualização, é adicionado o novo tipo de serviço para a aplicação implementada. Tal uma atualização não afeta qualquer uma das instâncias do serviço que já fizeram parte da aplicação, mas tem de ser criada uma instância do tipo de serviço que foi adicionada para o novo tipo de serviço ser o Active Directory (consulte [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Da mesma forma, os tipos de serviço podem ser removidos de uma aplicação como parte de uma atualização. No entanto, todas as instâncias de serviço do to-be-removidas do tipo de serviço tem de ser removidas antes de continuar com a atualização (consulte [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Modo de atualização manual
> [!NOTE]
> O *monitorizados* o modo de atualização é recomendado para todas as atualizações do Service Fabric.
> O *UnmonitoredManual* o modo de atualização deve ser considerado apenas para atualizações com falhas ou suspensas. 
>
>

Na *monitorizados* modo, o Service Fabric aplica políticas de estado de funcionamento para se certificar de que a aplicação está em bom estado, como o andamento da atualização. Se as diretivas de integridade são violadas, em seguida, a atualização está suspenso ou automaticamente revertida consoante especificado *FailureAction*.

Na *UnmonitoredManual* modo, o administrador do aplicativo tem total controle sobre a progressão da atualização. Esse modo é útil quando aplicar políticas de avaliação do Estado de funcionamento personalizado ou execução de atualizações não convencional para ignorar completamente a monitorização de estado de funcionamento (por exemplo, o aplicativo já está a perda de dados). Uma atualização executado no modo será suspenso após a conclusão de cada UD e explicitamente a deve ser retomada a usando [retomar ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Quando uma atualização está suspenso e estiver pronto para ser retomar pelo utilizador, apresentará o estado de atualização *RollforwardPending* (consulte [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Por fim, o *UnmonitoredAuto* modo é útil para a realização de iterações rápidas de atualização durante o desenvolvimento de serviço ou de teste, uma vez que sem intervenção do utilizador e não existem políticas de estado de funcionamento da aplicação são avaliadas.

## <a name="upgrade-with-a-diff-package"></a>Atualizar com um pacote de diff
Em vez de aprovisionamento de um pacote de aplicação completa, as atualizações também podem ser executadas por diff pacotes que contêm apenas os pacotes de código/configuração/dados atualizados, juntamente com o manifesto de aplicativo completo e concluir os manifestos de serviço de aprovisionamento. Pacotes de aplicação completa só são necessários para a instalação inicial de um aplicativo para o cluster. As atualizações subsequentes podem ser de pacotes de aplicação completa ou diff pacotes.  

Qualquer referência no manifesto do aplicativo ou manifestos de serviço de um pacote de diff que não pode ser encontrado no pacote de aplicação é substituída automaticamente com a versão atualmente aprovisionada.

Cenários para utilizar um pacote de diff são:

* Quando tiver um pacote de aplicações grandes que faz referência a vários arquivos de manifesto de serviço e/ou vários pacotes do código, pacotes de configuração ou pacotes de dados.
* Quando tiver um sistema de implementação que gera o layout de compilação diretamente a partir da sua aplicação processo de compilação. Neste caso, mesmo que o código não mudou, acabou de criar assemblies obtém uma soma de verificação diferente. Usando um pacote de aplicação completo solicitará que atualizar a versão em todos os pacotes de código. Usando um pacote de diff, apenas fornecer os ficheiros que foram alterados e os arquivos de manifesto em que a versão foi alterada.

Quando um aplicativo for atualizado com o Visual Studio, um pacote de diff é publicado automaticamente. Para criar um pacote de diff manualmente, o manifesto da aplicação e manifestos de serviço tem de ser atualizados, mas apenas os pacotes alterados devem ser incluídos no pacote de aplicação final.

Por exemplo, vamos começar com a aplicação seguinte (números de versão fornecidos para facilitar a compreensão):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Vamos supor que quisesse atualizar apenas o pacote do código de service1 usando um pacote de diff. A aplicação atualizada tem as seguintes alterações de versão:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Neste caso, atualize o manifesto do aplicativo para 2.0.0 e o manifesto de serviço para service1 refletir a atualização do pacote de código. A pasta para o seu pacote de aplicação teria a seguinte estrutura:

```text
app1/
  service1/
    code/
```

Em outras palavras, normalmente a criar um pacote de aplicação completa, em seguida, remova quaisquer pastas de pacote de código/configuração/dados para o qual a versão não foi alterada.

## <a name="rolling-back-application-upgrades"></a>A reverter as atualizações de aplicações

Embora as atualizações podem ser efetuadas o rollforward de uma das três modos (*monitorizados*, *UnmonitoredAuto*, ou *UnmonitoredManual*), eles podem apenas ser revertidos em qualquer um dos *UnmonitoredAuto* ou *UnmonitoredManual* modo. Sem interrupção de volta *UnmonitoredAuto* modo funciona da mesma forma que avançar com a exceção de que o valor predefinido de *UpgradeReplicaSetCheckTimeout* é diferente – consulte [aplicação Atualizar parâmetros](service-fabric-application-upgrade-parameters.md). Sem interrupção de volta *UnmonitoredManual* modo funciona da mesma forma que avanço – a reversão será suspenso após a conclusão de cada UD e deve ser explicitamente foi retomada com [ Retomar ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) para continuar com a reversão.

Reversões podem ser acionados automaticamente quando as políticas de estado de funcionamento de uma atualização na *monitorizados* modo com um *FailureAction* de *reversão* são violada (consulte [Parâmetros da atualização da aplicação](service-fabric-application-upgrade-parameters.md)) ou explicitamente utilizando [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante a reversão, o valor de *UpgradeReplicaSetCheckTimeout* e o modo de ainda pode ser alterado a qualquer momento através de [atualização ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Passos Seguintes
[Atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md) explica-lhe uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) explica-lhe uma atualização da aplicação com o PowerShell.

Controlar a forma como a aplicação seja atualizada com o [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Tornar as atualizações de aplicações compatíveis, aprendendo a usar [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Corrigir problemas comuns em atualizações da aplicação ao referir-se os passos no [resolução de problemas de atualizações de aplicações](service-fabric-application-upgrade-troubleshooting.md).
