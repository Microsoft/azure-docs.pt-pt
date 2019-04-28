---
title: Cenários comuns do Catálogo de Dados do Azure
description: Uma visão geral dos cenários comuns para o catálogo de dados Azure, incluindo o registo e deteção de origens de dados de elevado valor, permitindo a business intelligence personalizada e capturar conhecimento existente de origens de dados e processos.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: e95cc64b9086a6fb4c5e2d42521a5fd3f44244ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003961"
---
# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns do Catálogo de Dados do Azure
Este artigo apresenta cenários comuns que o catálogo de dados do Azure pode ajudar sua organização obter mais valor das suas fontes de dados existente.

## <a name="scenario-1-registration-of-central-data-sources"></a>Cenário 1: Registo de origens de dados central
As organizações têm, muitas vezes, várias origens de dados de elevado valor. Estas origens de dados incluem sistemas (OLTP), os armazéns de dados e bases de dados do business intelligence/análise de processamento de transações de linha de negócio, online. O número de sistemas e a sobreposição entre elas, normalmente aumenta ao longo do tempo, à medida que as empresas precisam evoluir e o próprio negócio evolui através de, por exemplo, fusões e aquisições.

Pode ser difícil para os membros da organização saber onde localizar os dados dentro destas origens de dados. Perguntas, como a seguir, são todas muito comuns:

* Dos sistemas de RH três utilizados dentro da empresa, que devo utilizar para criar este tipo de relatório?
* Onde devem ir para obter os números de vendas certificados para o ano fiscal que apenas terminou?
* Que posso fazer ou o que é o processo que devo usar para obter acesso ao armazém de dados?
* Não sei se esses números são adequados. Que posso fazer para obter informações sobre como estes dados deve ser usado para compartilhar este dashboard com minha equipe?

A estas e outras perguntas, o catálogo de dados do Azure pode fornecer respostas. As origens de dados central, alto valor, gerenciado por TI que são utilizadas pelas organizações costumam ser o ponto de partida lógico para povoar o catálogo. Embora qualquer utilizador pode registar uma origem de dados, ter o catálogo kick-started com as origens de dados que são mais prováveis fornecer o valor para o maior número de utilizadores que ajuda a conduzir a adoção e utilização do sistema. 

Se estiver a começar no catálogo de dados do Azure, identificar e registar origens de dados fundamentais utilizadas por várias equipas diferentes de consumidores de dados podem ser o primeiro passo para o sucesso.

Este cenário apresenta também uma oportunidade para anotar origens de dados de alto valor para torná-los mais fáceis de compreender e de acesso. É um aspecto-chave deste esforço incluir informações sobre como os utilizadores podem pedir acesso à origem de dados. Com o catálogo de dados do Azure, pode fornecer o endereço de e-mail do utilizador ou equipa responsável para controlar o acesso de origem de dados, links para ferramentas existentes ou documentação ou texto livre que descreve o processo de pedido de acesso. Estas informações ajudam os membros que detetar origens de dados registados, mas que ainda não tem permissões para acessar os dados facilmente pedir acesso ao utilizar os processos que estão definidos e controlados pelos proprietários de origem de dados.

## <a name="scenario-2-self-service-business-intelligence"></a>Cenário 2: Business intelligence personalizada
Embora as soluções de business intelligence empresariais tradicionais continuam a ser uma parte inestimável da finalização panoramas de dados de muitas organizações, a alteração ritmo dos negócios fez BI de autoatendimento mais importantes. Ao usar o BI de autoatendimento, os operadores de informações e os analistas podem criar seus próprios relatórios, livros e dashboards sem depender de uma equipa de TI central ou a ser restringidas pelo agendamento e a disponibilidade da equipe de TI.

Em cenários de BI de autoatendimento, os usuários normalmente combinam dados de várias origens, muitos dos quais podem não ter sido utilizados anteriormente para análise e BI. Embora algumas dessas fontes de dados poderão ser já conhecidas, ele pode ser um desafio para descobrir o que fazer para localizar e avaliar possíveis origens de dados para uma determinada tarefa.

Tradicionalmente, este processo de deteção é manual: analistas usar suas conexões de rede ponto a ponto para identificar outras pessoas que trabalham com os dados que está a ser procurados. Depois de uma origem de dados é encontrada e utilizada, o processo repete-se novamente para cada self-service BI esforço subseqüente, com vários utilizadores a executar um processo manual com redundância de deteção.

Com o catálogo de dados do Azure, a sua organização pode quebrar este ciclo de esforço. Após a deteção de uma origem de dados através de meios tradicionais, um analista pode de registá-lo para torná-lo mais facilmente Detetáveis por outros utilizadores no futuro. Embora o analista pode adicionar mais valor ao anotar recursos de dados registados, esta anotação não é necessário ter lugar ao mesmo tempo, como de registo. Os utilizadores podem contribuir ao longo do tempo, como permitir suas agendas, adicionando gradualmente o valor para as origens de dados registadas no catálogo.

Este crescimento orgânico do conteúdo do catálogo é um complemento natural para o registo inicial de origens de dados central. Preencher previamente o catálogo de dados que irão precisar de muitos usuários pode ser uma motivação para utilização inicial e a deteção. Permitir que os utilizadores para se registrar e anotar origens de adicionais pode ser uma forma de mantê-los e outros membros da organização envolvidos.

Vale a pena observar que embora este cenário centra-se especificamente sobre BI de autoatendimento, os mesmos padrões e desafios aplicam-se a empresa BI projetos de grande escala, bem. Ao utilizar o catálogo de dados, a sua organização pode melhorar qualquer esforço que envolve um processo manual de deteção de origem de dados.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Cenário 3: Capturar conhecimentos restritos
Como sabe quais os dados que precisa fazer seu trabalho e onde encontrar esses dados?

Se já esteve no seu trabalho durante algum tempo, provavelmente sabe. Já passou por um processo de aprendizagem de gradual e ao longo do tempo, ter aprendido sobre as origens de dados fundamentais para o seu trabalho diário.

Quando um novo funcionário associa à sua equipa, como essa pessoa sabe quais dados são necessários para a tarefa e onde encontrá-lo?

É provável, a nova pessoa vem até com essas perguntas.

Esta transferência em curso de conhecimentos restritos é a parte do processo de deteção da origem de dados em pequenas e grandes organizações. Mais experiente e qualificado os membros da Equipe acumuladas conhecimento ao longo dos anos, e os membros da Equipe mais recente aprenderam a peça-lhe quando têm dúvidas. As informações mais importantes, muitas vezes, existem apenas na cabeça de algumas pessoas essenciais e, quando essas pessoas estiverem em férias ou deixam a equipe, a organização sofre.

Especialistas em dados normalmente faça um esforço para documentar seu conhecimento, partilhá-lo por e-mail ou em documentos do Word num site de equipa do SharePoint. Embora essa abordagem pode ser útil, ele introduz um novo problema de Deteção: como as pessoas saibam quais documentação existe e onde encontrá-lo?

Com o catálogo de dados do Azure, a sua organização tiver um local central único para armazenar e compartilhar esse conhecimento tribal e para fazer facilmente detetável. No catálogo de dados, as especialistas de dados podem anotar recursos de dados diretamente e fornecem ligações para documentação existente. Quando os membros da organização utilizam o catálogo para detetar uma origem de dados, eles vão encontrá não só a origem em si, mas também o conhecimento que existia anteriormente apenas nas mentes dos especialistas da sua organização.
