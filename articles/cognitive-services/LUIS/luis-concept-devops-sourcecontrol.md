---
title: Ramos de controlo e desenvolvimento de fontes - LUIS
description: Como manter a sua aplicação de Compreensão linguística (LUIS) sob controlo de origem. Como aplicar atualizações a uma aplicação LUIS enquanto trabalha num ramo de desenvolvimento.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 25f2c4f4698785326f80c24d3749e7585e85d5bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309511"
---
# <a name="devops-practices-for-luis"></a>Práticas de DevOps para LUIS

Os engenheiros de software que estão a desenvolver uma aplicação de Compreensão de Idiomas (LUIS) podem aplicar práticas de DevOps em torno do [controlo de origem,](luis-concept-devops-sourcecontrol.md) [construções automatizadas,](luis-concept-devops-automation.md) [testes](luis-concept-devops-testing.md)e [gestão de lançamentos](luis-concept-devops-automation.md#release-management) seguindo estas orientações.

## <a name="source-control-and-branch-strategies-for-luis"></a>Estratégias de controlo de fontes e de sucursais para o LUIS

Um dos factores-chave de que o sucesso dos DevOps depende é do [controlo de fontes.](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops) Um sistema de controlo de fontes permite que os desenvolvedores colaborem no código e rastreiem as alterações. O uso de sucursais permite que os desenvolvedores alterem entre diferentes versões da base de código, e trabalhem independentemente de outros membros da equipa. Quando os desenvolvedores levantam um pedido de [puxar](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) para propor atualizações de um ramo para outro, ou quando as alterações são fundidas, estas podem ser o gatilho para [construções automatizadas](luis-concept-devops-automation.md) para construir e testar continuamente código.

Ao utilizar os conceitos e orientações descritos neste documento, pode desenvolver uma aplicação LUIS enquanto rastreia alterações num sistema de controlo de fontes e siga estas melhores práticas de engenharia de software:

- **Controlo de Origem**
  - O código fonte para a sua aplicação LUIS está num formato legível pelo homem.
  - O modelo pode ser construído a partir da fonte de uma forma repetível.
  - O código fonte pode ser gerido por um repositório de código fonte.
  - Credenciais e segredos como a autoria e as teclas de subscrição nunca são armazenados em código fonte.

- **Ramificação e fusão**
  - Os desenvolvedores podem trabalhar a partir de agências independentes.
  - Os desenvolvedores podem trabalhar em vários ramos simultaneamente.
  - É possível integrar alterações a uma aplicação LUIS de uma sucursal para outra através da rebase ou fusão.
  - Os desenvolvedores podem fundir um PR com a filial principal.

- **Controlo de versões**
  - Cada componente de uma grande aplicação deve ser versão independente, permitindo que os desenvolvedores detetem alterações ou atualizações de rutura apenas olhando para o número da versão.

- **Revisões de Código**
  - As alterações no PR são apresentadas como código fonte legível humano que pode ser revisto antes de aceitar o PR.

## <a name="source-control"></a>Controlo de código fonte

Para manter a [definição](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition) de esquema de aplicação de uma aplicação LUIS num sistema de gestão de código fonte, utilize o [formato LUDown ( `.lu` )](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)  representação da aplicação. `.lu` o formato é preferido para `.json` o formato porque é legível pelo homem, o que facilita a revisão e a revisão de alterações nos PRs.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Guarde uma aplicação LUIS utilizando o formato LUDown

Para guardar uma aplicação LUIS em `.lu` formato e colocá-la sob controlo de origem:

- TAMBÉM: [Exporte a versão](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions) da aplicação a partir do portal `.lu` [LUIS](https://www.luis.ai/) e adicione-a ao seu repositório de controlo de origem

- OR: Use um editor de texto para criar um `.lu` ficheiro para uma aplicação LUIS e adicione-o ao seu repositório de controlo de origem

> [!TIP]
> Se estiver a trabalhar com a exportação JSON de uma aplicação LUIS, pode [convertê-la para LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) utilizando o [BotBuilder-Tools LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS). Utilize a `--sort` opção para garantir que as intenções e as expressões sejam ordenadas alfabeticamente.  
> Note que o **. A** capacidade de exportação da LU incorporada no portal LUIS já separa a saída.

### <a name="build-the-luis-app-from-source"></a>Construa a app LUIS a partir de fonte

Para uma aplicação LUIS, construir a partir de meios *de origem* para [criar uma nova versão da app LUIS importando a `.lu` fonte,](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) [para treinar a versão](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e[publicá-la.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) Pode fazê-lo no portal LUIS, ou na linha de comando:

- Utilize o portal LUIS para [importar a `.lu` versão](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) da app do controlo de origem e [treine](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [publique](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) a app.

- Utilize a [Interface de Linha de Comando-Quadro bot para LUIS](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) na linha de comando ou num fluxo de trabalho CI/CD para [importar](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) a versão da app do controlo `.lu` de origem para uma aplicação LUIS, e [treinar](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) e [publicar](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) a app.

### <a name="files-to-maintain-under-source-control"></a>Ficheiros para manter sob controlo de origem

Os seguintes tipos de ficheiros para a sua aplicação LUIS devem ser mantidos sob controlo de origem:

- `.lu` arquivo para o pedido LUIS

- [Ficheiros de definição de teste de unidade](luis-concept-devops-testing.md#writing-tests) (expressões e resultados esperados)

- [Ficheiros de teste de lote](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format) (expressões e resultados esperados) utilizados para testes de desempenho

### <a name="credentialsand-keys-are-not-checked-in"></a>Credenciais e chaves não são verificados

Não inclua chaves de subscrição ou valores confidenciais semelhantes em ficheiros que faça check-in no seu repo onde possam ser visíveis a pessoal não autorizado. As teclas e outros valores que deve impedir de fazer o check-in incluem:

- Chaves de autoria e previsão da LUIS
- Pontos finais de autoria e previsão da LUIS
- Chaves de assinatura Azure
- Fichas de acesso, como o símbolo de um [diretor de serviço](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) Azure usado para autenticação automação

#### <a name="strategies-for-securely-managing-secrets"></a>Estratégias para gerir segredos de forma segura

As estratégias para gerir segredos de forma segura incluem:

- Se estiver a utilizar o controlo da versão Git, pode armazenar segredos de tempo de execução num ficheiro local e impedir o check-in do ficheiro adicionando um padrão que combine o nome de ficheiro com um ficheiro [.gitignore](https://git-scm.com/docs/gitignore)
- Num fluxo de trabalho de automação, pode armazenar segredos de forma segura na configuração de parâmetros oferecida por essa tecnologia de automação. Por exemplo, se estiver a utilizar [as ações do GitHub,](https://github.com/features/actions)pode armazenar segredos de forma segura nos [segredos do GitHub.](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)

## <a name="branching-and-merging"></a>Ramificação e fusão

Sistemas de controlo de versão distribuídos como o Git dão flexibilidade na forma como os membros da equipa publicam, partilham, revejam e iteram sobre as alterações de código através de agências de desenvolvimento partilhadas com outras. Adote uma [estratégia de ramificação de Git](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance) que seja apropriada para a sua equipa.

Qualquer que seja a estratégia de ramificação que adote, um princípio fundamental de todos eles é que os membros da equipa podem trabalhar na solução dentro de um ramo de *recurso* independentemente do trabalho que está a decorrer noutros ramos.

Apoiar o trabalho independente em sucursais com um projeto LUIS:

- **O ramo principal tem a sua própria app LUIS.** Esta aplicação representa o estado atual da sua solução para o seu projeto e a sua versão ativa atual deve sempre mapear para a `.lu` fonte que está no ramo principal. Todas as atualizações para a `.lu` fonte desta aplicação devem ser revistas e testadas para que esta aplicação possa ser implementada para construir ambientes como a Produção a qualquer momento. Quando as atualizações ao `.lu` mercado forem fundidas em mestre a partir de um ramo de funcionalidades, deverá criar uma nova versão na aplicação LUIS e [aumentar o número da versão](#versioning).

- **Cada ramo de recurso deve utilizar a sua própria instância de uma aplicação LUIS**. Os desenvolvedores trabalham com esta aplicação numa sucursal de funcionalidades sem risco de afetar os desenvolvedores que estão a trabalhar em outros balcões. Esta aplicação 'dev branch' é uma cópia de trabalho que deve ser eliminada quando o ramo de funcionalidades é eliminado.

![Ramo de recurso Git](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Os desenvolvedores podem trabalhar a partir de agências independentes

Os desenvolvedores podem trabalhar em atualizações numa aplicação LUIS independentemente de outras agências por:

1. Criar um ramo de recurso a partir do ramo principal (dependendo da estratégia do seu ramo, geralmente dominar ou desenvolver).

1. [Criar uma nova aplicação LUIS no portal LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app) (a " app*dev branch*") apenas para apoiar o trabalho no ramo de funcionalidades.

   * Se a fonte para a `.lu` sua solução já existe no seu ramo, porque foi guardada após o trabalho feito noutra sucursal no início do projeto, crie a sua app dev branch LUIS importando o `.lu` ficheiro.

   * Se está a começar a trabalhar num novo projeto, ainda não terá a fonte para a `.lu` sua app master LUIS no repo. Irá criar o `.lu` ficheiro exportando a sua aplicação de filial dev a partir do portal quando tiver concluído o seu trabalho de filial de funcionalidade, e submetê-lo como parte do seu PR.

1. Trabalhe na versão ativa da sua aplicação de filial dev para implementar as alterações necessárias. Recomendamos que trabalhe apenas numa única versão da sua aplicação de filial dev para todo o trabalho da filial de funcionalidades. Se criar mais de uma versão na sua aplicação de filial dev, tenha cuidado para rastrear qual a versão que contém as alterações que pretende fazer quando levantar o seu PR.

1. Teste as atualizações - consulte [testes para LUIS DevOps](luis-concept-devops-testing.md) para obter detalhes sobre o teste da sua aplicação de filial dev.

1. Exporte a versão ativa da sua aplicação de dev `.lu` a partir da lista de [versões.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)

1. Faça o check-in das suas atualizações e convide a revisão dos seus comentários. Se estiver a usar o GitHub, vai levantar um [pedido de retirada.](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)

1. Quando as alterações forem aprovadas, misture as atualizações no ramo principal. Neste momento, irá criar uma nova [versão](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) da app *master* LUIS, utilizando a atualizada `.lu` em master. Consulte [a versão](#versioning) para obter considerações sobre a definição do nome da versão.

1. Quando o ramo de funcionalidades é eliminado, é uma boa ideia apagar a aplicação de dev branch LUIS que criou para o trabalho da filial de funcionalidades.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Os desenvolvedores podem trabalhar em vários ramos simultaneamente

Se seguir o padrão acima descrito nos [Desenvolvedores pode funcionar a partir de ramos independentes,](#developers-can-work-from-independent-branches)então utilizará uma aplicação LUIS única em cada ramo de recurso. Um único desenvolvedor pode trabalhar em vários ramos simultaneamente, desde que mudem para a aplicação de dev filial correta LUIS para a sucursal em que estão atualmente a trabalhar.

Recomendamos que utilize o mesmo nome tanto para o ramo de funcionalidades como para a aplicação de dev branch LUIS que cria para o trabalho da filial de funcionalidades, para tornar menos provável que funcione acidentalmente na aplicação errada.

Como referido acima, recomendamos que, para a simplicidade, trabalhe numa única versão em cada aplicação de filial dev. Se estiver a utilizar várias versões, tenha o cuidado de ativar a versão correta à medida que alterna entre aplicações de dev branch.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Vários desenvolvedores podem trabalhar no mesmo ramo simultaneamente

Pode suportar vários desenvolvedores que trabalham no mesmo ramo de funcionalidades ao mesmo tempo:

- Os desenvolvedores verificam o mesmo ramo de funcionalidades e empurram e puxam as alterações submetidas por si e por outros desenvolvedores enquanto o trabalho prossegue, como é normal.

- Se seguir o padrão acima descrito nos [Desenvolvedores pode funcionar a partir de ramos independentes,](#developers-can-work-from-independent-branches)então este ramo usará uma aplicação LUIS única para apoiar o desenvolvimento. Esta aplicação 'dev branch' LUIS será criada pelo primeiro membro da equipa de desenvolvimento que começa a trabalhar no ramo de funcionalidades.

- [Adicione os membros da equipa como contribuintes](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate) para a app dev branch LUIS.

- Quando o trabalho da sucursal estiver concluído, exporte a versão ativa da aplicação DEV branch LUIS `.lu` a partir da lista de [versões](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions), guarde o ficheiro atualizado `.lu` no repo, e faça o check-in e PR as alterações.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Incorporação de alterações de um ramo para outro com rebase ou fusão

Alguns outros desenvolvedores da sua equipa que trabalham noutro ramo podem ter feito atualizações à `.lu` fonte e fundi-los ao ramo principal depois de ter criado o seu ramo de recurso. Pode querer incorporar as suas alterações na sua versão de trabalho antes de continuar a fazer alterações no seu ramo de funcionalidades. Pode fazê-lo [rebaseou ou fundiu-se para dominar](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) da mesma forma que qualquer outro ativo de código. Uma vez que a aplicação LUIS em formato LUDown é legível pelo homem, suporta a fusão utilizando ferramentas de fusão padrão.

Siga estas dicas se estiver a rebasar a sua aplicação LUIS num ramo de funcionalidades:

- Antes de rebasear ou fundir, certifique-se de que a sua cópia local da origem da `.lu` sua aplicação tem todas as suas últimas alterações que aplicou através do portal LUIS, reexportação da sua app a partir do portal primeiro. Assim, pode certificar-se de que quaisquer alterações que tenha feito no portal e ainda não exportadas não se percam.

- Durante a fusão, utilize ferramentas padrão para resolver quaisquer conflitos de fusão.

- Não se esqueça que depois de a rebase ou fusão estar completa para re importar a app de volta para o portal, para que esteja a trabalhar com a app atualizada à medida que continua a aplicar as suas próprias alterações.

### <a name="merge-prs"></a>Fundir PRs

Depois de o seu Pr ser aprovado, pode fundir as suas alterações no seu ramo principal. Não se aplicam considerações especiais à fonte LUDown para uma aplicação LUIS: é legível pelo homem e por isso suporta a fusão utilizando ferramentas de fusão padrão. Quaisquer conflitos de fusão podem ser resolvidos da mesma forma que com outros ficheiros de origem.

Depois de o seu RP ter sido fundido, é recomendado para a limpeza:

- Apague o ramo no seu repo

- Elimine a aplicação LUIS 'dev branch' que criou para o trabalho da filial de recurso.

Da mesma forma que com os ativos do código de aplicação, deve escrever testes unitários para acompanhar as atualizações da aplicação LUIS. Deve utilizar fluxos de trabalho de integração contínua para testar:

- Atualizações num PR antes da fusão do PR
- A aplicação master branch LUIS depois de um PR ter sido aprovado e as alterações foram fundidas em mestre.

Para obter mais informações sobre os testes para LUIS DevOps, consulte [Testes de DevOps para LUIS](luis-concept-devops-testing.md). Para obter mais detalhes sobre a implementação de fluxos de trabalho, consulte [os fluxos de trabalho da Automação para LUIS DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Revisões de código

Uma aplicação LUIS em formato LUDown é legível para o homem, que suporta a comunicação de alterações num PR adequado para revisão. Os ficheiros de teste de unidade também são escritos no formato LUDown e também facilmente revistos num RP.

## <a name="versioning"></a>Controlo de versões

Uma aplicação consiste em vários componentes que podem incluir coisas como um bot em execução no [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0), [QnA Maker,](https://www.qnamaker.ai/) [Azure Speech service](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), e muito mais. Para atingir o objetivo de aplicações pouco acopladas, utilize o controlo de [versão](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control) para que cada componente de uma aplicação seja versão independente, permitindo que os desenvolvedores detetem alterações ou atualizações de rutura apenas olhando para o número da versão. É mais fácil ver a sua app LUIS independentemente de outros componentes se a mantiver no seu próprio repo.

A aplicação LUIS para o ramo principal deve ter um esquema de versão aplicada. Quando fundir atualizações para `.lu` uma app LUIS em master, irá então importar essa fonte atualizada para uma nova versão na app LUIS para o ramo principal.

Recomenda-se que utilize um esquema de versão numérica para a versão da aplicação master LUIS, por exemplo:

`major.minor[.build[.revision]]`

Cada atualização o número da versão é incrementado no último dígito.

A versão principal/menor pode ser usada para indicar o âmbito das alterações à funcionalidade da aplicação LUIS:

* Versão Principal: Uma mudança significativa, como o suporte para uma nova [Intenção](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent) ou [Entidade](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types)
* Versão menor: Uma pequena alteração compatível com retro-compatível, como após uma nova formação significativa
* Construir: Nenhuma alteração de funcionalidade, apenas uma construção diferente.

Depois de ter determinado o número de versão para a última revisão da sua app master LUIS, precisa de construir e testar a nova versão da aplicação, e publicá-la num ponto final onde pode ser usada em diferentes ambientes de construção, como Quality Assurance ou Production. É altamente recomendado que automatize todos estes passos num fluxo de trabalho de integração contínua (CI).

Veja:
- [Fluxos de trabalho de automatização](luis-concept-devops-automation.md) para detalhes sobre como implementar um fluxo de trabalho de CI para testar e lançar uma aplicação LUIS.
- [Gestão de Lançamento](luis-concept-devops-automation.md#release-management) para obter informações sobre como implementar a sua aplicação LUIS.

### <a name="versioning-the-feature-branch-luis-app"></a>Versão da app LUIS 'feature branch'

Quando estiver a trabalhar com uma aplicação LUIS 'dev branch' que criou para apoiar o trabalho numa sucursal de recursos, estará a exportar a sua app quando o seu trabalho estiver concluído e incluirá a atualização `'lu` no seu RP. A sucursal no seu repo, e a app 'dev branch' LUIS devem ser eliminadas após a fusão do PR em mestre. Uma vez que esta aplicação existe apenas para suportar o trabalho no ramo de funcionalidades, não existe um esquema específico de versão que necessite de aplicar dentro desta app.

Quando as suas alterações no seu PR são fundidas em mestre, é quando a versão deve ser aplicada, de modo que todas as atualizações para master são versadas de forma independente.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [sobre testes para LUIS DevOps](luis-concept-devops-testing.md)
* Saiba como [implementar DevOps para LUIS com GitHub](luis-how-to-devops-with-github.md)
