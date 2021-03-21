---
title: Fluxos de trabalho contínuos para apps LUIS
description: Como implementar fluxos de trabalho ci/CD para DevOps for Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932538"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Integração Contínua e Fluxos de Trabalho de Entrega Contínua para DEVOps LUIS

Os engenheiros de software que estão a desenvolver uma aplicação de Compreensão de Idiomas (LUIS) podem aplicar práticas de DevOps em torno do controlo de [fontes, construções automatizadas,](luis-concept-devops-automation.md) [testes](luis-concept-devops-testing.md)e [gestão de lançamentos.](luis-concept-devops-automation.md#release-management) [](luis-concept-devops-sourcecontrol.md) Este artigo descreve conceitos para implementar construções automatizadas para o LUIS.

## <a name="build-automation-workflows-for-luis"></a>Construir fluxos de trabalho de automação para o LUIS

![Fluxos de trabalho ci](./media/luis-concept-devops-automation/luis-automation.png)

No seu sistema de gestão de código fonte (SCM), configurar gasodutos de construção automatizados para executar nos seguintes eventos:

1. **Fluxo de trabalho de relações** públicas desencadeado quando um [pedido de puxar](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) é levantado. Este fluxo de trabalho valida o conteúdo do PR *antes* de as atualizações serem fundidas no ramo principal.
1. **Fluxo de trabalho CI/CD** desencadeado quando as atualizações são empurradas para o ramo principal, por exemplo, após a fusão das alterações a partir de um PR. Este fluxo de trabalho garante a qualidade de todas as atualizações para o ramo principal.

O **fluxo de trabalho CI/CD** combina dois processos complementares de desenvolvimento:

* [Integração Contínua](/azure/devops/learn/what-is-continuous-integration) (CI) é a prática de engenharia de comprometer frequentemente código num repositório partilhado, e realizar uma construção automatizada sobre ele. Em conjugação com uma abordagem automatizada de [testes,](luis-concept-devops-testing.md) a integração contínua permite-nos verificar que para cada atualização, a fonte LUDown ainda é válida e pode ser importada para uma app LUIS, mas também que passa por um grupo de testes que verificam que a app treinada pode reconhecer as intenções e entidades necessárias para a sua solução.

* [A Entrega Contínua](/azure/devops/learn/what-is-continuous-delivery) (CD) leva ainda mais o conceito de Integração Contínua para implantar automaticamente a aplicação num ambiente onde é possível fazer testes mais aprofundados. O CD permite-nos aprender cedo sobre quaisquer problemas imprevistos que surjam das nossas mudanças o mais rapidamente possível, e também para aprender sobre lacunas na nossa cobertura de teste.

O objetivo da integração contínua e da entrega contínua é garantir que "o principal é sempre expedidor",. Para uma aplicação LUIS, isto significa que poderíamos, se necessário, pegar em qualquer versão da aplicação principal luis e enviá-la em produção.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Ferramentas para construir fluxos de trabalho de automação para o LUIS

Existem diferentes tecnologias de automação de construção disponíveis para criar fluxos de trabalho de automação. Todos eles requerem que você possa script etapas usando uma interface de linha de comando (CLI) ou chamadas REST para que possam executar em um servidor de construção.

Utilize as seguintes ferramentas para a construção de fluxos de trabalho de automatização para o LUIS:

* [Bot Framework Tools LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) para trabalhar com apps e versões LUIS, treinar, testar e publicá-las dentro do serviço LUIS.

* [Azure CLI](/cli/azure/) para consultar subscrições da Azure, buscar chaves de autoria e previsão luis, e criar um [principal de serviço](/cli/azure/ad/sp) Azure usado para a autenticação de automação.

* [A NLU. Ferramenta DevOps](https://github.com/microsoft/NLU.DevOps) para [testar uma aplicação LUIS](luis-concept-devops-testing.md) e analisar os resultados dos testes.

### <a name="the-pr-workflow"></a>O fluxo de trabalho de Relações Públicas

Como mencionado, configura este fluxo de trabalho para executar quando um desenvolvedor levantar um PR para propor alterações a serem fundidos de uma filial de recurso para o ramo principal. O seu objetivo é verificar a qualidade das mudanças no Pr antes de serem fundidas ao ramo principal.

Este fluxo de trabalho deve:

* Criar uma app LUIS temporária importando a `.lu` fonte no PR.
* Treine e publique a versão da aplicação LUIS.
* Executar todos os [testes da unidade](luis-concept-devops-testing.md) contra ele.
* Passe o fluxo de trabalho se todos os testes passarem, caso contrário falhar.
* Limpe e elimine a aplicação temporária.

Se for suportado pelo seu SCM, configufique as regras de proteção do ramo para que este fluxo de trabalho seja concluído com sucesso antes de o PR poder ser concluído.

### <a name="the-main-branch-cicd-workflow"></a>O fluxo de trabalho ci/CD do ramo principal

Configure este fluxo de trabalho a ser executado após a fusão das atualizações no RP no ramo principal. O seu objetivo é manter a barra de qualidade para o seu ramo principal alta testando as atualizações. Se as atualizações encontrarem a barra de qualidade, este fluxo de trabalho implementa a nova versão da aplicação LUIS para um ambiente onde pode fazer testes mais aprofundados.

Este fluxo de trabalho deve:

* Construa uma nova versão na sua aplicação principal LUIS (a app que mantém para o ramo principal) utilizando o código fonte atualizado.

* Treine e publique a versão da aplicação LUIS.

  > [!NOTE]
  > Como explicado nos [testes de execução num fluxo de trabalho de construção automatizada,](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) deve publicar a versão da aplicação LUIS em teste para que ferramentas como a NLU. DevOps pode acessá-lo. A LUIS suporta apenas duas slots de publicação nomeadas, *encenação* e *produção* para uma aplicação LUIS, mas também pode [publicar uma versão diretamente](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) e [consultar por versão](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name). Utilize a publicação de versão direta nos fluxos de trabalho da sua automatização para evitar limitar-se a utilizar as ranhuras de publicação nomeadas.

* Escorram todos os [testes de unidade.](luis-concept-devops-testing.md)

* Opcionalmente, realizar [testes de lote](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) para medir a qualidade e precisão da versão da aplicação LUIS e compará-la com alguma linha de base.

* Se os testes estiverem concluídos com sucesso:
  * Marque a fonte no repo.
  * Executar o trabalho de Entrega Contínua (CD) para implementar a versão da aplicação LUIS em ambientes para mais testes.

### <a name="continuous-delivery-cd"></a>Entrega contínua (CD)

O trabalho de CD num fluxo de trabalho CI/CD funciona condicionalmente com o sucesso dos testes de unidade de construção e automatização. O seu trabalho é implantar automaticamente a aplicação LUIS num ambiente onde pode fazer mais testes.

Não há uma solução recomendada sobre a melhor forma de implementar a sua app LUIS, e tem de implementar o processo que é apropriado para o seu projeto. O [repo de modelo LUIS DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) implementa uma solução simples para isso que é publicar a nova versão da app [LUIS](./luis-how-to-publish-app.md) para a slot de publicação de *produção.* Isto é bom para uma simples armação. No entanto, se necessitar de suportar vários ambientes de produção diferentes ao mesmo tempo, como o *desenvolvimento*, *encenação* e *UAT*, então o limite de duas faixas de publicação nomeadas por app será insuficiente.

Outras opções para implementar uma versão de aplicação incluem:

* Deixe a versão da aplicação publicada no ponto final da versão direta e implemente um processo para configurar ambientes de produção a jusante com o ponto final da versão direta, conforme necessário.
* Mantenha diferentes aplicativos LUIS para cada ambiente de produção e escreva medidas de automação para importar a `.lu` nova versão na app LUIS para o ambiente de produção alvo, para treinar e publicá-la.
* Exporte a versão da aplicação LUIS testada num [recipiente de estivador LUIS](./luis-container-howto.md?tabs=v3) e desloque o recipiente LUIS para [instâncias do Contentor](../../container-instances/index.yml)Azure .

## <a name="release-management"></a>Gestão de Versões

Geralmente recomendamos que faça entregas contínuas apenas aos seus ambientes de não produção, tais como desenvolvimento e encenação. A maioria das equipas requer um processo manual de revisão e aprovação para implantação num ambiente de produção. Para uma implantação de produção, talvez queira certificar-se de que isso acontece quando pessoas-chave da equipa de desenvolvimento estão disponíveis para apoio, ou durante períodos de tráfego reduzido.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [implementar DevOps para LUIS com GitHub](luis-how-to-devops-with-github.md)
* Aprenda a escrever um fluxo de [trabalho gitHub actions com nlu. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
