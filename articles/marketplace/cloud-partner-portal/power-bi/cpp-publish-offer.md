---
title: Publicar oferta de aplicação do Power BI - Azure Marketplace | Documentos da Microsoft
description: Publica uma oferta de aplicação do Power BI no mercado do Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822757"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicar uma oferta de aplicação do Power BI

A última etapa, depois de ter definido uma oferta no Portal de parceiros de nuvem e criado os ativos técnicos associados, é submeter a oferta para publicação. Para iniciar este processo, no painel esquerdo dos **nova oferta** janela, selecione **Publish**. Para obter mais informações, consulte [ofertas de publicar o Azure Marketplace e AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Passos de publicação

Estes são os principais passos no processo de publicação:

![Etapas de processo de publicação para a aplicação do Power BI oferecem](./media/publishing-process-steps.png)

Esta tabela descreve cada passo e fornece seu tempo de conclusão estimada:

|   Etapa de publicação            |   Hora     |   Descrição                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validar a pré-requisitos       | 15 minutos     | Oferecer informações e oferecer definições são validadas.                            |
| Certificação                | 1 a 7 dias   | A equipa de certificação do Power BI analisa a sua oferta. A equipe executa a aplicação do Power BI através de um teste de verificação manual ao instalar a aplicação através do URL de instalação fornecido. Validações primárias são realizadas como parte do processo de certificação de aplicações (descrito mais adiante neste documento).         |
| Empacotamento                    | \< 1 hora  | Ativos de técnicos da oferta são empacotados para uso do cliente.                        |
| Levar o registo de geração | \< 1 hora  | Levar sistemas são configurados e implementados.                                      |
| Aprovação do publicador            | \-         | Concluir uma revisão final e a confirmação antes da oferta entra no ar. Agora também terá uma ligação para a sua oferta de pré-visualização. Depois que estiver satisfeito com o aspeto de pré-visualização, selecione **Go Live** sobre o **estado** separador. Esta ação envia um pedido para a equipa de integração para listar a sua aplicação no AppSource.    |
| Em direto                         | \< 3 horas | A oferta é agora listada publicamente ("em direto") no AppSource, e os clientes podem ver a sua aplicação e implementá-lo em suas assinaturas do Power BI. Também receberá um e-mail de confirmação. Na coluna da direita no **todas as ofertas** guia, pode ver o estado de todas as suas ofertas. Sobre o **estado** guia, pode ver o estado de fluxo de publicação detalhadas para a sua oferta. |
|   |   |

Permitir que o máximo de oito dias para este processo ser concluído. Depois de seguir estes passos de publicação, a oferta de aplicação do Power BI será listada na [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) secção de aplicações do Power BI.


### <a name="app-certification-process"></a>Processo de certificação da aplicação

A equipa de integração da Microsoft usa esse processo para validar a sua submissão de oferta de aplicação do Power BI:

1. Reveja as ligações de ajuda e documentos legais.
2. Valide informações de contacto de suporte.
3. Utilize o URL do instalador para verificar a instalação adequada.
4. Analise a aplicação de software maligno e outro conteúdo malicioso.
5. Certifique-se de que o conteúdo apresentado corresponde à descrição da aplicação.
6. Certifique-se de que as operações relacionadas com a aplicação funcionam conforme esperado no Power BI. A equipe abre-se de relatórios e dashboards com dados de exemplo, se liga a origens de dados personalizados, atualiza os dados e assim por diante.

A equipe de certificação fornece comentários se encontrarem estes problemas.  Para obter mais informações sobre os requisitos de aplicações do Power BI, consulte a [documentação de aplicações do Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Passos Seguintes

Recomendamos que monitorize regularmente a sua aplicação no [marketplace do AppSource](https://appsource.microsoft.com).  Também deve usar o [Insights vendedor](../../cloud-partner-portal-orig/si-getting-started.md) recurso do [Cloud Partner Portal](https://cloudpartner.azure.com/#insights) para obter informações sobre os clientes do marketplace e a utilização da aplicação. Por fim, pode [atualizar a sua oferta](./cpp-update-existing-offer.md).
