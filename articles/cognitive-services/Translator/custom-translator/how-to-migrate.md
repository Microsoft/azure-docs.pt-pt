---
title: Migrar o espaço de trabalho e projetos do Microsoft Tradutor Hub? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explica como migrar o seu espaço de trabalho do Hub e projeta para o Azure Cognitive Services Custom Tradutor.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5334168081ea18650bd6afd2411e076952e08749
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537991"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrar espaço de trabalho do Hub e projetos para Tradutor Personalizado

Pode migrar facilmente o seu espaço de trabalho microsoft [Translator Hub](https://hub.microsofttranslator.com/) e projetos para Custom Tradutor. A migração é iniciada a partir do Microsoft Hub selecionando um espaço de trabalho ou projeto, selecionando um espaço de trabalho em Custom Tradutor e, em seguida, selecionando as formações que pretende transferir. Após o início da migração, as definições de formação selecionadas serão transferidas com todos os documentos relevantes. Os modelos implantados são treinados e podem ser enviados automaticamente após a conclusão.

Estas ações são realizadas durante a migração:
* Todos os documentos e definições de projeto terão os seus nomes transferidos com a adição de "hub_" prefixados no nome. Os dados de teste e afinação gerados automaticamente serão nomeados hub_systemtune_ \<modelid> ou hub_systemtest_ \<modelid> .
* Quaisquer formações que estivessem no estado implantado quando a migração ocorrer serão automaticamente treinadas utilizando os documentos da formação do Hub. Esta formação não será cobrada à sua assinatura. Se a implantação automática for selecionada para a migração, o modelo treinado será implementado após a conclusão. Serão aplicadas taxas regulares de hospedagem.
* Quaisquer treinos migrados que não estivessem no estado implantado serão colocados no projeto de estado migrado. Neste estado, terá a opção de treinar um modelo com a definição migratória, mas aplicar-se-ão taxas regulares de formação.
* Em qualquer ponto, a pontuação BLEU migrada do treino do Hub pode ser encontrada na página TrainingDetails do modelo na rubrica "Bleu score in MT Hub".

> [!Note] 
> Para que uma formação tenha sucesso, o Tradutor Personalizado requer um mínimo de 10.000 frases extraídas únicas. O Tradutor Personalizado não pode realizar um treino com menos do que o [mínimo sugerido.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)

## <a name="find-custom-translator-workspace-id"></a>Encontre o ID do espaço de trabalho do tradutor personalizado

Para migrar o espaço de trabalho [do Microsoft Tradutor Hub,](https://hub.microsofttranslator.com/) precisa de ID de espaço de trabalho de destino em Tradutor Personalizado. O espaço de trabalho de destino em Custom Tradutor é para onde todos os seus espaços de trabalho e projetos hub devem ser migrados.

Encontrará o seu destino Workspace ID na página De Configurações do Tradutor Personalizado:

1. Aceda à página "Definição" no portal 'Tradução' personalizado.

2. Encontrará o ID do Espaço de Trabalho na secção Informação Básica.

    ![Como encontrar o ID do espaço de trabalho do destino](media/how-to/how-to-find-destination-ws-id.png)

3. Mantenha o seu destino Workspace ID para se referir durante o processo de migração.

## <a name="migrate-a-project"></a>Migrar um projeto

Se quiser migrar os seus projetos seletivamente, o Microsoft Tradutor Hub dá-lhe essa capacidade.

Para migrar um projeto:

1. Inscreva-se no Microsoft Tradutor Hub.

2. Vá à página "Projetos".

3. Clique no link "Migrar" para o projeto apropriado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub.png)

4. Ao premir o link migratório, será apresentado um formulário que lhe permite:
   * Especifique o espaço de trabalho para o qual pretende transferir no Tradutor Personalizado
   * Indique se deseja transferir todos os treinos com treinos bem sucedidos ou apenas com os treinos implantados. Por predefinição, todos os treinos bem sucedidos serão transferidos.
   * Indique se gostaria que o seu carro de treino fosse acionado quando o treino terminar. Por predefinição, o seu treino não será acionado automaticamente após a conclusão.

5. Clique em "Submeter pedido".

## <a name="migrate-a-workspace"></a>Migrar um espaço de trabalho

Além de migrar um único projeto, você também pode migrar todos os projetos com formações bem sucedidas em um espaço de trabalho. Isto fará com que cada projeto no espaço de trabalho seja avaliado como se a ligação migratória tivesse sido pressionada. Esta funcionalidade é adequada para utilizadores com muitos projetos que pretendam migrar todos eles para Custom Tradutor com as mesmas definições. Uma migração de espaço de trabalho pode ser iniciada a partir da página de definições do Centro tradutor.

Para migrar um espaço de trabalho:

1. Inscreva-se no Microsoft Tradutor Hub.

2. Aceda à página "Definições".

3. Na página "Definições" clique em "Migrar dados do espaço de trabalho para tradutor personalizado".

    ![Como migrar do Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na página seguinte selecione qualquer uma destas duas opções:

    a. Apenas treinos implementados: Selecionar esta opção migrará apenas os seus sistemas implantados e documentos relacionados.

    b. Todas as Formações Bem Sucedidas: Selecionar esta opção migrará todos os seus treinos e documentos relacionados com sucesso.

    c. Insira o seu destino Workspace ID em Tradutor Personalizado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Clique em Enviar Pedido.

## <a name="migration-history"></a>História da Migração

Quando tiver solicitado a migração do espaço de trabalho/ projeto do Hub, encontrará o seu histórico de migração na página De Definições de Tradutor Personalizado.

Para ver a história da migração, siga estes passos:

1. Aceda à página "Definição" no portal 'Tradução' personalizado.

2. Na secção Histórico de Migração da página Definições, clique em Histórico de Migração.

    ![História da migração](media/how-to/how-to-migration-history.png)

A página de Histórico de Migração apresenta a seguinte informação como resumo de cada migração que solicitou.

1. Migrado Por: Nome e e-mail do utilizador submetido este pedido de migração

2. Migrado Em: Data e hora da migração

3. Projetos: Número de projetos solicitados para a migração v/s número de projetos migrados com sucesso.

4. Formações: Número de formações solicitadas para a migração v/s número de formações migradas com sucesso migraram.

5. Documentos: O número de documentos solicitados para a migração v/s número de documentos migrados com sucesso migraram.

    ![Detalhes da história da migração](media/how-to/how-to-migration-history-details.png)

Se quiser um relatório de migração mais detalhado sobre os seus projetos, formações e documentos, tem detalhes de exportação de opções como CSV.

## <a name="implementation-notes"></a>Notas de implementação
* Sistemas com pares de idiomas AINDA não disponíveis no Custom Tradutor só estarão disponíveis para aceder a dados ou despreocupação através do Custom Tradutor. Estes projetos serão marcados como "Indisponível" na página projetos. À medida que possibilitamos novos pares linguísticos com o Custom Tradutor, os projetos tornar-se-ão ativos para treinar e implementar. 
* A migração de um projeto de Hub para Custom Tradutor não terá qualquer impacto nas suas formações ou projetos do Hub. Não apagamos projetos ou documentos do Hub durante uma migração e não despreocupamos modelos.
* Só pode migrar uma vez por projeto. Se precisar repetir uma migração num projeto, contacte-nos.
* O Custom Tradutor suporta pares de idiomas NMT de e para inglês. [Veja a lista completa de línguas suportadas.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization) O Hub não requer modelos de base e, portanto, suporta vários milhares de línguas. Pode migrar um par de línguas não suportado, no entanto, apenas realizaremos a migração de documentos e definições de projetos. Não poderemos treinar o novo modelo. Além disso, estes documentos e projetos serão apresentados como inativos, de modo a indicar que não podem ser usados neste momento. Se o apoio for adicionado a estes projetos e/ou documentos, estes tornar-se-ão ativos e treináveis.
* O Tradutor Personalizado não suporta atualmente dados de formação monolingues. Tal como os pares linguísticos não apoiados, pode migrar documentos monolingues, mas eles mostram-se inativos até que os dados monolingues sejam suportados.
* O Tradutor Personalizado requer 10 mil frases paralelas para treinar. O Microsoft Hub poderia treinar num conjunto menor de dados. Se for migrada uma formação que não satisfaça este requisito, não será treinada.

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado contra Hub

Esta tabela compara as funcionalidades entre o Microsoft Tradutor Hub e o Custom Tradutor.

| Funcionalidade | Hub | Custom Translator |
| ------- | :-: | :---------------: |
| Estado do recurso de personalização    | Disponibilidade Geral    | Disponibilidade Geral |
| Versão API de texto    | V2     | V3  |
| Personalização SMT    | Sim    | Não |
| Personalização de NMT    | Não    | Sim |
| Nova personalização unificada de serviços de fala    | Não    | Sim |
| Sem vestígios | Sim | Sim |

## <a name="new-languages"></a>Novas línguas

Se você é uma comunidade ou organização que trabalha na criação de um novo sistema linguístico para tradutor, procure [custommt@microsoft.com](mailto:custommt@microsoft.com) mais informações.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo.](how-to-train-model.md)
- Comece a utilizar o seu modelo de tradução personalizado implantado através [do Tradutor V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
