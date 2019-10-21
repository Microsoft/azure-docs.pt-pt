---
title: Migrar o espaço de trabalho e os projetos do Hub do Microsoft Translator? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Migre seu espaço de trabalho de Hub e projetos para o tradutor personalizado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 9c97eaef30a08123605ed4c01810745e312d6aa0
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675504"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrar o espaço de trabalho do Hub e projetos para o tradutor personalizado

Você pode facilmente migrar seu espaço de trabalho do [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) e projetos para o tradutor personalizado. A migração é iniciada do Hub da Microsoft selecionando um espaço de trabalho ou projeto, selecionando um espaço de trabalho no Tradutor personalizado e, em seguida, selecionando os treinamentos que você deseja transferir. Depois que a migração for iniciada, as configurações de treinamento selecionadas serão transferidas com todos os documentos relevantes. Modelos implantados são treinados e podem ser autoimplantados após a conclusão.

Essas ações são executadas durante a migração:
* Todos os documentos e definições de projeto terão seus nomes transferidos com a adição de "hub_" prefixado para o nome. Os dados de teste e de ajuste gerados automaticamente serão nomeados hub_systemtune_ \<modelid > ou hub_systemtest_ \<modelid >.
* Todos os treinamentos que estavam no estado implantado quando a migração ocorrer serão treinados automaticamente usando os documentos do treinamento do Hub. Este treinamento não será cobrado em sua assinatura. Se a implantação automática foi selecionada para a migração, o modelo treinado será implantado após a conclusão. Os encargos de hospedagem regular serão aplicados.
* Todos os treinamentos migrados que não estavam no estado implantado serão colocados no estado de rascunho migrado. Nesse estado, você terá a opção de treinar um modelo com a definição migrada, mas os encargos de treinamento regular serão aplicados.
* A qualquer momento, a pontuação de BLEU migrada do treinamento de Hub pode ser encontrada na página TrainingDetails do modelo no cabeçalho "Pontuação Bleu no Hub MT".

> [!Note] 
> Para que um treinamento tenha sucesso, o tradutor personalizado requer um mínimo de 10.000 frases extraídas exclusivas. O tradutor personalizado não pode conduzir um treinamento com menos do que o [mínimo sugerido](sentence-alignment.md#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Localizar ID do espaço de trabalho do tradutor personalizado

Para migrar o espaço de trabalho [Hub do Microsoft Translator](https://hub.microsofttranslator.com/) , você precisa da ID do espaço de trabalho de destino no Tradutor personalizado. O espaço de trabalho de destino no Tradutor personalizado é onde todos os seus espaços de trabalho e projetos de Hub devem ser migrados para o.

Você encontrará a ID do espaço de trabalho de destino na página Configurações do tradutor personalizado:

1. Vá para a página "configuração" no portal do tradutor personalizado.

2. Você encontrará a ID do espaço de trabalho na seção informações básicas.

    ![Como localizar a ID do espaço de trabalho de destino](media/how-to/how-to-find-destination-ws-id.png)

3. Mantenha a ID do espaço de trabalho de destino para se referir ao processo de migração.

## <a name="migrate-a-project"></a>Migrar um projeto

Se você quiser migrar seus projetos seletivamente, o Hub do Microsoft Translator lhe dará essa capacidade.

Para migrar um projeto:

1. Entre no Hub do Microsoft Translator.

2. Vá para a página "projetos".

3. Clique no link "migrar" para o projeto apropriado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub.png)

4. Ao pressionar o link migrar, você verá um formulário que permite:
   * Especifique o espaço de trabalho que você deseja transferir para o tradutor personalizado
   * Indique se você deseja transferir todos os treinamentos com treinamentos bem-sucedidos ou apenas os treinamentos implantados. Por padrão, todos os treinamentos bem-sucedidos serão transferidos.
   * Indique se você deseja que seu treinamento seja implantado automaticamente quando o treinamento for concluído. Por padrão, seu treinamento não será implantado automaticamente após a conclusão.

5. Clique em "Enviar solicitação".

## <a name="migrate-a-workspace"></a>Migrar um espaço de trabalho

Além de migrar um único projeto, você também pode migrar todos os projetos com treinamentos bem-sucedidos em um espaço de trabalho. Isso fará com que cada projeto no espaço de trabalho seja avaliado como se o link de migração tivesse sido pressionado. Esse recurso é adequado para usuários com muitos projetos que desejam migrar todos eles para o tradutor personalizado com as mesmas configurações. Uma migração de espaço de trabalho pode ser iniciada na página Configurações do Hub do tradutor.

Para migrar um espaço de trabalho:

1. Entre no Hub do Microsoft Translator.

2. Vá para a página "configurações".

3. Na página "configurações", clique em "migrar dados de espaço de trabalho para o tradutor personalizado".

    ![Como migrar do Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na próxima página, selecione uma destas duas opções:

    a. Somente treinamentos implantados: a seleção desta opção migrará apenas seus sistemas implantados e documentos relacionados.

    b. Todos os treinamentos bem-sucedidos: a seleção dessa opção irá migrar todos os seus treinamentos e documentos relacionados bem-sucedidos.

    c. Insira a ID do espaço de trabalho de destino no Tradutor personalizado.

    ![Como migrar do Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Clique em Enviar solicitação.

## <a name="migration-history"></a>Histórico de migração

Quando tiver solicitado a migração de espaço de trabalho/projeto do Hub, você encontrará o histórico de migração na página Configurações do tradutor personalizado.

Para exibir o histórico de migração, siga estas etapas:

1. Vá para a página "configuração" no portal do tradutor personalizado.

2. Na seção Histórico de migração da página configurações, clique em histórico de migração.

    ![Histórico de migração](media/how-to/how-to-migration-history.png)

A página Histórico de migração exibe as informações a seguir como resumo para cada migração solicitada.

1. Migrado por: nome e email do usuário enviou esta solicitação de migração

2. Migrado em: carimbo de data e hora da migração

3. Projetos: número de projetos solicitados para o número de projetos de migração do v/s migrados com êxito.

4. Treinamentos: número de treinamentos solicitados para o número de treinamentos de migração de v/s migrados com êxito.

5. Documentos: o número de documentos solicitados para a migração de documentos do número v/s migrados com êxito.

    ![Detalhes do histórico de migração](media/how-to/how-to-migration-history-details.png)

Se você quiser um relatório de migração mais detalhado sobre seus projetos, treinamentos e documentos, você tem a opção Exportar detalhes como CSV.

## <a name="implementation-notes"></a>Notas de implementação
* Sistemas com pares de idiomas ainda não disponíveis no Tradutor personalizado só estarão disponíveis para acessar dados ou Desimplantar por meio de um tradutor personalizado. Esses projetos serão marcados como "indisponíveis" na página projetos. À medida que habilitarmos novos pares de idiomas com o tradutor personalizado, os projetos ficarão ativos para treinar e implantar. 
* A migração de um projeto do hub para o tradutor personalizado não terá nenhum impacto sobre os treinamentos ou projetos de Hub. Não excluímos projetos ou documentos do hub durante uma migração e não implantamos modelos.
* Você só tem permissão para migrar uma vez por projeto. Se você precisar repetir uma migração em um projeto, entre em contato conosco.
* O tradutor personalizado dá suporte a pares de idiomas NMT de e para inglês. [Veja a lista completa de idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). O Hub não requer modelos de linha de base e, portanto, dá suporte a vários milhares de idiomas. Você pode migrar um par de idiomas sem suporte, no entanto, executaremos apenas a migração de documentos e definições de projeto. Não será possível treinar o novo modelo. Além disso, esses documentos e projetos serão exibidos como inativos para indicar que eles não podem ser usados no momento. Se o suporte for adicionado a esses projetos e/ou documentos, eles ficarão ativos e poderão ser treinados.
* Atualmente, o tradutor personalizado não oferece suporte a dados de treinamento multilíngue. Como pares de idiomas sem suporte, você pode migrar documentos multilíngues, mas eles aparecem como inativos até que haja suporte para dados monomultilíngues.
* O tradutor personalizado requer as sentenças paralelas de 10 k para treinar. O Hub da Microsoft poderia treinar em um conjunto menor de dados. Se um treinamento for migrado, o que não atende a esse requisito, ele não será treinado.

## <a name="custom-translator-versus-hub"></a>Tradutor personalizado versus Hub

Esta tabela compara os recursos entre o Hub do Microsoft Translator e o tradutor personalizado.

|   | 82801ER | Custom Translator |
|:-----|:----:|:----:|
|Status do recurso de personalização   | Disponibilidade Geral  | Disponibilidade Geral |
| Versão da API de texto  | V2    | V2  |
| Personalização de SMT | Sim   | Não |
| Personalização de NMT | Não    | Sim |
| Nova personalização de serviços de fala unificada | Não    | Sim |
| Sem Rastreio | Sim | Sim |

## <a name="new-languages"></a>Novos idiomas

Se você for uma comunidade ou organização trabalhando na criação de um novo sistema de linguagem para o Microsoft Translator, acesse [custommt@microsoft.com](mailto:custommt@microsoft.com) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

- [Treinar um modelo](how-to-train-model.md).
- Comece a usar seu modelo de tradução personalizada implantado por meio [do Microsoft API de tradução de texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
