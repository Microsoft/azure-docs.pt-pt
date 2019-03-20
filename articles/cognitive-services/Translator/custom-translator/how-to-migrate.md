---
title: Migrar a área de trabalho do Microsoft Translator Hub e projetos? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Migre a sua área de trabalho do Hub e projetos ao Translator personalizado.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 3b2c5f3e02241f8c4e8a9ae87bd7436863243978
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901530"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrar os projetos de área de trabalho de Hub e ao Translator personalizado

Pode migrar facilmente seus [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) projetos ao Translator personalizado e de área de trabalho. É iniciada a migração do Microsoft Hub ao selecionar uma área de trabalho ou projeto, em seguida, selecionar uma área de trabalho no Translator personalizado e, em seguida, selecionar os treinamentos que pretende transferir. Depois de inicia a migração, as definições de treinamento selecionados serão transferidas com todos os documentos relevantes. Modelos implementados estão treinados e podem ser autodeployed após a conclusão.

Estas ações são executadas durante a migração:
* Todos os documentos e as definições de projeto terão seus nomes transferidas com a adição de "hub_", o prefixo ao nome. Teste gerado automaticamente e a otimização de dados terá o nome hub_systemtune_\<modelid > ou hub_systemtest_\<modelid >.
* Qualquer treinamentos que estavam em estado de implementação quando a migração ocorre automaticamente serão preparados com os documentos do treinamento de Hub. Esta formação não será cobrada à sua subscrição. Se implementar o automática tiver sido selecionada para a migração, o modelo treinado será implementado após a conclusão. Será aplicada regular custos de hospedagem.
* Qualquer treinamentos migrados que não estavam em estado de implementação serão colocados sobre o estado de rascunho migrados. Neste estado, terá a opção de preparar um modelo com a definição migrada, mas serão aplicáveis os custos de treinamento regular.
* Em qualquer momento, a pontuação de BLEU migrados do Hub de treinamento pode ser encontrado na página TrainingDetails do modelo no "Bleu pontuação no Hub de MT" cabeçalho.

> [!Note]
> Para um treinamento tenha êxito, o Translator personalizado requer um mínimo de 10 000 frases extraídos exclusivos. Tradutor personalizado não é possível realizar um treinamento têm menos do que o [mínima sugerida](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences).

## <a name="find-custom-translator-workspace-id"></a>Localizar o ID de área de trabalho do Microsoft Translator personalizado

Para migrar [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) área de trabalho, terá de destino. o ID de área de trabalho no Translator personalizado. A área de trabalho de destino no Translator personalizado é onde todas as suas áreas de trabalho de Hub e os projetos devem ser migrados para.

Encontrará sua ID de área de trabalho de destino na página de definições do Microsoft Translator personalizadas:

1. Aceda à página de "Definição" no portal do Translator personalizado.

2. Encontrará o ID de área de trabalho na seção informações básicas.

    ![Como localizar o ID de área de trabalho de destino](media/how-to/how-to-find-destination-ws-id.png)

3. Mantenha o seu ID de área de trabalho para fazer referência durante o processo de migração de destino.

## <a name="migrate-a-project"></a>Migrar um projeto

Se pretender migrar seus projetos de forma seletiva, o Microsoft Translator Hub dá-lhe essa capacidade.

Para migrar um projeto:

1. Inicie sessão no Hub de Microsoft Translator.

2. Aceda à página de "Projects".

3. Clique em "Migrar" link para o projeto apropriado.

    ![Como migrar a partir do Hub](media/how-to/how-to-migrate-from-hub.png)

4. Ao premir a ligação de migrar será apresentado um formulário que lhe permite:
   * Especifique a área de trabalho que pretende transferir para no Translator personalizado
   * Indique se pretende transferir todos os treinamentos com os treinamentos com êxito ou apenas os treinamentos implementados. Por predefinição serão transferidos todos os treinamentos com êxito.
   * Indique se pretende sua automática de treinamento implementada quando terminar de treinamento. Por predefinição seu treinamento não será automaticamente implantado após a conclusão.

5. Clique em "Submeter o pedido".

## <a name="migrate-a-workspace"></a>Migrar uma área de trabalho

Além da migração de um único projeto, também pode migrar todos os projetos com treinamentos com êxito numa área de trabalho. Isso fará com que cada projeto na área de trabalho a ser avaliado como se a ligação de migrar tinha foi pressionada. Esta funcionalidade é adequada para os utilizadores com muitos projetos que pretendem migrar todos eles ao Translator personalizado com as mesmas definições. Uma migração de área de trabalho pode ser iniciada a partir da página de definições do Hub de Microsoft Translator.

Para migrar um espaço de trabalho:

1. Inicie sessão no Hub de Microsoft Translator.

2. Aceda à página "Definições".

3. Na página "Definições" clique em "Área de trabalho para migrar dados ao Translator personalizada".

    ![Como migrar a partir do Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na página seguinte, selecione uma destas duas opções:

    a. Apenas Treinamentos implementados: A seleção desta opção irá migrar apenas os sistemas implantados e os documentos relacionados.

    b. Todos os Treinamentos com êxito: A seleção desta opção irá migrar todos os treinamentos com êxito e documentos relacionados.

    c. Introduza o seu ID de área de trabalho de destino no Translator personalizado.

    ![Como migrar a partir do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Clique em Submeter pedido.

## <a name="migration-history"></a>Histórico de migração

Quando pediu a área de trabalho / migração a partir do Hub do projeto, encontrará o histórico de migração na página de definições do Microsoft Translator personalizadas.

Para ver o histórico de migração, siga estes passos:

1. Aceda à página de "Definição" no portal do Translator personalizado.

2. Na secção de histórico de migração da página de definições, clique em histórico de migração.

    ![Histórico de migração](media/how-to/how-to-migration-history.png)

Página de histórico de migração apresenta seguindo as informações de resumo para cada pedido de migração.

1. Migrados: Nome e e-mail do utilizador submeter este pedido de migração

2. Migrados: Carimbo de data e hora da migração

3. Projetos: Número de projetos solicitado para o número de v/s de migração de projetos migrado com êxito.

4. Treinamentos: Número de treinamentos solicitado para o número de v/s de migração de treinamentos migrado com êxito.

5. Documentos: O número de documentos solicitado para o número de v/s de migração de documentos migrado com êxito.

    ![Detalhes do histórico de migração](media/how-to/how-to-migration-history-details.png)

Se quiser que mais detalhadas que o relatório de migração sobre os seus projetos, treinamentos e documentos, terá opção Exportar detalhes como CSV.

## <a name="implementation-notes"></a>Notas de implementação
* Migrando um projeto do Hub para o Translator personalizado não terá qualquer impacto no seu treinamentos de Hub ou projetos. Não eliminamos projetos ou documentos do Hub durante uma migração e não anular a implementação modelos.
* Apenas são permitidos para migrar uma vez por projeto. Se precisar repetir uma migração num projeto, entre em contato conosco.
* Personalizado Translator suporta pares de idiomas NMT de e para inglês. [Ver a lista completa de langauges suportados](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Hub não necessita de modelos de linha de base e, portanto, oferece suporte a várias linguagens de milhares. É possível migrar um par de idioma não suportado, no entanto, estamos apenas irá executar a migração de documentos e as definições do projeto. Não será capazes de preparar o modelo de novo. Além disso, estes documentos e projetos serão apresentados como Inativos para indicar que não pode ser utilizados neste momento. Se é adicionado suporte para estes projetos e/ou documentos, ficam trainable e Active Directory.
* Tradutor personalizado não suporta atualmente os dados de treinamento monolíngüe. Como pares de idioma não suportado, pode migrar monolíngües documentos, mas eles mostram como Inativos até que os dados monolíngües são suportados.
* Tradutor personalizado requer 10 frases paralela de k para treinar. Hub da Microsoft poderia dar formação num conjunto menor de dados. Se um treinamento é migrado que não cumpre este requisito, ele não será preparado.

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado em comparação com o Hub

Esta tabela compara as funcionalidades entre o Microsoft Translator Hub e o Translator personalizado.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Estado da funcionalidade de personalização   | Disponibilidade Geral  | Disponibilidade Geral |
| Versão de API de texto  | V2    | V3  |
| Personalização de SMT | Sim   | Não |
| Personalização de NMT | Não    | Sim |
| Nova personalização de serviços de voz unificada | Não    | Sim |
| Sem Rastreio | Sim | Sim |

## <a name="new-languages"></a>Novos idiomas

Se for uma Comunidade ou organização trabalhar sobre como criar um novo sistema de idioma para o Microsoft Translator, entrar em contacto para [ custommt@microsoft.com ](mailto:custommt@microsoft.com) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

- [Preparar um modelo](how-to-train-model.md).
- Começar a utilizar o seu modelo de tradução personalizadas implementado via [API de texto do Microsoft Translator V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
