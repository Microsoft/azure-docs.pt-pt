---
title: Migrar espaço de trabalho do Microsoft Tradutor Hub e projetos? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explica como migrar o seu espaço de trabalho hub e projetos para o Tradutor Personalizado de Serviços Cognitivos Azure.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2fa90a8099778bf37ce8534e968a2b1b4345c2d8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75446773"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Espaço de trabalho migrate Hub e projetos para tradutor personalizado

Pode migrar facilmente o espaço de trabalho do [Microsoft Tradutor Hub](https://hub.microsofttranslator.com/) e projetos para tradutor personalizado. A migração é iniciada a partir do Microsoft Hub selecionando um espaço de trabalho ou projeto, selecionando um espaço de trabalho em Tradutor Personalizado e, em seguida, selecionando os treinos que pretende transferir. Após o início da migração, as definições de formação selecionadas serão transferidas com todos os documentos relevantes. Os modelos implantados são treinados e podem ser autoimplantados após a conclusão.

Estas ações são realizadas durante a migração:
* Todos os documentos e definições de projeto terão os seus nomes transferidos com a adição de "hub_" pré-fixado seleções ao nome. Os dados de teste e afinação gerados automaticamente serão nomeados hub_systemtune_\<> modelídico ou hub_systemtest_\<> modelóide.
* Quaisquer formações que estivessem no estado de implantação quando a migração ocorre serão automaticamente treinadas usando os documentos da formação do Hub. Este treino não será cobrado à sua subscrição. Se a implementação automática for selecionada para a migração, o modelo treinado será implantado após a conclusão. Serão aplicadas taxas regulares de hospedagem.
* Quaisquer formações migradas que não estivessem no estado destacado serão colocadas no projeto de estado migrado. Neste estado, terá a opção de treinar um modelo com a definição migrada, mas serão aplicados encargos de formação regulares.
* Em qualquer momento, a pontuação BLEU migrada do treino do Hub pode ser encontrada na página TrainingDetails do modelo na rubrica "Bleu score in MT Hub".

> [!Note] 
> Para que um treino tenha sucesso, o Tradutor Personalizado requer um mínimo de 10.000 frases extraídas únicas. Tradutor personalizado não pode realizar um treino com menos do que o [mínimo sugerido](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Encontre o ID do espaço de trabalho do tradutor personalizado

Para migrar o espaço de trabalho do [Microsoft Tradutor Hub,](https://hub.microsofttranslator.com/) precisa de ID do espaço de trabalho de destino em Tradutor Personalizado. O espaço de trabalho de destino em Tradutor Personalizado é para onde todos os seus espaços de trabalho hub e projetos serão migrados.

Encontrará o id do espaço de trabalho do seu destino na página de Definições de Tradutor Personalizado:

1. Vá à página "Definição" no portal tradutor personalizado.

2. Encontrará o ID do espaço de trabalho na secção Informação Básica.

    ![Como encontrar id do espaço de trabalho de destino](media/how-to/how-to-find-destination-ws-id.png)

3. Mantenha o seu id do espaço de trabalho de destino para se referir durante o processo de migração.

## <a name="migrate-a-project"></a>Migrar um projeto

Se quiser migrar os seus projetos seletivamente, o Microsoft Tradutor Hub dá-lhe essa capacidade.

Para migrar um projeto:

1. Inscreva-se no Microsoft Tradutor Hub.

2. Vá à página de "Projetos".

3. Clique no link "Migrar" para um projeto apropriado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub.png)

4. Ao premir a ligação de migração, será-lhe apresentado um formulário que lhe permite:
   * Especifique o espaço de trabalho para o qual pretende transferir em Tradutor Personalizado
   * Indique se deseja transferir todos os treinos com treinos bem sucedidos ou apenas com os treinos implementados. Por defeito, todos os treinos bem sucedidos serão transferidos.
   * Indique se gostaria que o seu treino fosse implantado quando o treino terminar. Por defeito, o seu treino não será implantado automaticamente após a conclusão.

5. Clique em "Enviar pedido".

## <a name="migrate-a-workspace"></a>Migrar um espaço de trabalho

Além de migrar um único projeto, também poderá migrar todos os projetos com formações bem sucedidas num espaço de trabalho. Isto fará com que cada projeto no espaço de trabalho seja avaliado como se a ligação migratória tivesse sido pressionada. Esta funcionalidade é adequada para utilizadores com muitos projetos que querem migrar todos para O Tradutor Personalizado com as mesmas configurações. Uma migração do espaço de trabalho pode ser iniciada a partir da página de definições do Tradutor Hub.

Para migrar um espaço de trabalho:

1. Inscreva-se no Microsoft Tradutor Hub.

2. Vá para a página "Definições".

3. Na página "Definições" clique em "Migrar dados do espaço de trabalho para tradutor personalizado".

    ![Como migrar do Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na página seguinte selecione qualquer uma destas duas opções:

    a. Treinos implantados apenas: A seleção desta opção irá migrar apenas os seus sistemas implantados e documentos relacionados.

    b. Todos os Treinos bem sucedidos: A seleção desta opção irá migrar todos os seus treinos e documentos relacionados com sucesso.

    c. Insira o seu id do espaço de trabalho de destino em Tradutor Personalizado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Clique no Pedido de Submissão.

## <a name="migration-history"></a>História da Migração

Quando tiver solicitado espaço de trabalho/migração do projeto a partir do Hub, encontrará o seu histórico de migração na página Definições de Tradutorpersonalizado.

Para ver a história da migração, siga estes passos:

1. Vá à página "Definição" no portal tradutor personalizado.

2. Na secção História da Migração da página Definições, clique no Histórico de Migração.

    ![História da migração](media/how-to/how-to-migration-history.png)

A página do Histórico de Migração apresenta a seguinte informação como resumo de cada migração que solicitou.

1. Migrado Por: Nome e e-mail do utilizador submeteu este pedido de migração

2. Migrado On: Data e hora da migração

3. Projetos: Número de projetos solicitados para migração v/s número de projetos migrados com sucesso.

4. Formações: Número de formações solicitadas para migração v/s número de formações migradas com sucesso migrados.

5. Documentos: O número de documentos solicitados para migração v/s número de documentos migrados com sucesso.

    ![Detalhes do histórico da migração](media/how-to/how-to-migration-history-details.png)

Se quiser um relatório de migração mais detalhado sobre os seus projetos, formações e documentos, tem os detalhes de exportação de opção como CSV.

## <a name="implementation-notes"></a>Notas de Implementação
* Os sistemas com pares de idiomas AINDA não disponíveis no Tradutor Personalizado só estarão disponíveis para aceder a dados ou não ser implantados através de Tradutor Personalizado. Estes projetos serão marcados como "Indisponíveis" na página de Projetos. À medida que permitimos novos pares linguísticos com tradutor personalizado, os projetos tornar-se-ão ativos para treinar e implementar. 
* Migrar um projeto do Hub para o Tradutor Personalizado não terá qualquer impacto nos seus treinos ou projetos do Hub. Não apagamos projetos ou documentos do Hub durante uma migração e não desimplantamos modelos.
* Só é permitido migrar uma vez por projeto. Se precisar de repetir uma migração num projeto, por favor contacte-nos.
* O Custom Tradutor suporta pares de línguas NMT de e para inglês. [Ver a lista completa de línguas suportadas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). O Hub não requer modelos de base e, portanto, suporta vários milhares de línguas. Você pode migrar um par de línguas não suportados, no entanto, nós só realizaremos a migração de documentos e definições de projeto. Não poderemos treinar o novo modelo. Além disso, estes documentos e projetos serão apresentados como inativos de forma a indicar que não podem ser usados neste momento. Se for em conjunto apoio a estes projetos e/ou documentos, tornar-se-ão ativos e treináveis.
* O Custom Tradutor não suporta atualmente dados de formação monolingual. Tal como os pares de línguas não suportados, pode migrar documentos monolingues, mas mostram-se inativos até que os dados monolingues sejam suportados.
* Tradutor personalizado requer frases paralelas de 10k para treinar. O Microsoft Hub poderia treinar com um conjunto de dados menores. Se for migrada uma formação que não satisfaça este requisito, não será treinada.

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado versus Hub

Esta tabela compara as funcionalidades entre o Microsoft Tradutor Hub e o Custom Tradutor.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Estatuto de funcionalidade de personalização   | Disponibilidade Geral  | Disponibilidade Geral |
| Versão API de texto  | V2    | V3  |
| Personalização SMT | Sim   | Não |
| Personalização NMT | Não    | Sim |
| Nova personalização unificada dos serviços de Fala | Não    | Sim |
| Sem vestígios | Sim | Sim |

## <a name="new-languages"></a>Novas línguas

Se você é uma comunidade ou organização trabalhando na criação [custommt@microsoft.com](mailto:custommt@microsoft.com) de um novo sistema linguístico para o Microsoft Tradutor, procure mais informações.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo.](how-to-train-model.md)
- Comece a utilizar o seu modelo de tradução personalizada implementado através do [Microsoft Tradutor Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
