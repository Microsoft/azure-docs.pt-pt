---
title: Como configurar um Private Endpoint - QnA Maker
description: Compreenda a criação private Endpoint disponível na QnA Maker gerida.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710554"
---
# <a name="private-endpoints"></a>Pontos Finais Privados

O Ponto Final Privado do Azure é uma interface de rede que o liga a um serviço de forma privada e segura com a tecnologia Azure Private Link. Agora, o QnA Maker fornece-lhe suporte para criar pontos finais privados para o Serviço de Pesquisa Azure. Esta funcionalidade encontra-se disponível na QnA Maker gerida. 

Os pontos finais privados são fornecidos pela [Azure Private Link,](../../private-link/private-link-overview.md)como um serviço separado. Para obter mais informações sobre os custos, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Pré-requisitos
> [!div class="checklist"]
> * Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.
> * Um recurso [gerido](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) pela QnA Maker criado no portal Azure. Lembre-se do seu ID, Subscrição, nome de recurso QnA do Azure Ative que selecionou quando criou o recurso.

## <a name="steps-to-enable-private-endpoint"></a>Passos para permitir o ponto final privado
1. Atribua papel *de Contribuidor* ao serviço gerido pela QnA Maker na instância do Serviço de Pesquisa Azure. Esta operação requer acesso *do Proprietário* à subscrição. Vá ao separador Identidade no recurso de serviço para obter a identidade.
![Identidade de serviço gerido](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Adicione a identidade acima como Contributer indo ao *separador* IAM do Serviço de Pesquisa Azure. ![ Serviço gerido IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Clique em *Adicionar atribuições de funções,* adicione a identidade e clique em *Guardar*.
![Atribuição de função gerida](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Agora, vá ao *separador Networking* na instância do Serviço de Pesquisa Azure e altere os dados de conectividade Endpoint de *público* para *privado*. Esta operação é um processo de longa duração e pode demorar até 30 minutos para ser concluída. 
![Rede de pesquisa gerida Azure](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Aceda ao *separador Networking* do serviço gerido pela QnA Maker e no *âmbito do Acesso allow a partir de*, selecione as redes selecionadas e a opção de *pontos finais privados* e clique *em guardar*. 
![Gerido QnA fabricante de nova armazenaking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Isto estabelecerá uma ligação privada entre o serviço de fabricante qnA e a instância do serviço de pesquisa cognitiva Azure. Pode verificar a ligação de ponto final private no *separador 'Rede'* da instância do serviço de pesquisa Azure. Uma vez concluída toda a operação, é bom utilizar o seu serviço QnA Maker. 
![Serviço de Networking gerido](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Detalhes do suporte
 * Não suportamos alterar o Serviço de Pesquisa Azure uma vez que ativa o acesso privado ao seu serviço QnAMaker. Se alterar o Serviço de Pesquisa Azure através do separador 'Configuração' depois de ter ativado o acesso privado, o serviço QnAMaker tornar-se-á inutilizável.
 * Depois de estabelecer a Private Endpoint Connection, se mudar a rede de serviços de pesquisa Azure para 'Public', não poderá utilizar o serviço QnAMaker. A rede de serviços de pesquisa Azure tem de ser 'Privada' para a Ligação De Ponto Final Privado funcionar