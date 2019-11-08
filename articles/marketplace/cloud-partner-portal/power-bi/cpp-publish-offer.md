---
title: Publicar Power BI oferta de aplicativo | Azure Marketplace
description: Publicar uma oferta de aplicativo Power BI no Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: 324be960bd2d22623763ca3e24b99be92ff04174
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826041"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicar uma oferta de aplicativo Power BI

A última etapa, depois de definir uma oferta no Portal do Cloud Partner e criar os ativos técnicos associados, é enviar a oferta para publicação. Para iniciar esse processo, no painel esquerdo da janela **nova oferta** , selecione **publicar**. Para obter mais informações, consulte [publicar o Azure Marketplace e ofertas do AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Etapas de publicação

Estas são as principais etapas do processo de publicação:

![Etapas do processo de publicação para a oferta de aplicativo Power BI](./media/publishing-process-steps.png)

Esta tabela descreve cada etapa e fornece seu tempo de conclusão estimado:

|   Etapa de publicação            |   Hora     |   Descrição                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validar pré-requisitos       | 15 minutos     | As informações da oferta e as configurações da oferta são validadas.                            |
| Certificação                | 1-7 dias   | A equipe de certificação Power BI analisa sua oferta. A equipe executa seu aplicativo Power BI por meio de um teste de verificação manual instalando o aplicativo por meio da URL de instalação fornecida. As validações primárias são executadas como parte do processo de certificação do aplicativo (descrito mais adiante neste documento).         |
| Empacotamento                    | \< de 1 hora  | Os ativos técnicos da oferta são empacotados para uso do cliente.                        |
| Registro de geração de cliente potencial | \< de 1 hora  | Os sistemas de Lead são configurados e implantados.                                      |
| Aprovação do Publicador            | \-         | Você concluiu uma revisão e uma confirmação finais antes que a oferta fique ativa. Agora você também terá um link para visualizar sua oferta. Depois de ficar satisfeito com a aparência de sua visualização, **Selecione Ativar** na guia **status** . Isso envia uma solicitação para a equipe de integração para listar seu aplicativo no AppSource.    |
| Em direto                         | \< 3 horas | Sua oferta agora está listada publicamente ("Live") no AppSource, e os clientes podem exibir seu aplicativo e implantá-lo em suas assinaturas de Power BI. Você também receberá um email de confirmação. Na coluna à direita da guia **todas as ofertas** , você pode ver o status de todas as suas ofertas. Na guia **status** , você pode ver o status detalhado do fluxo de publicação para sua oferta. |
|   |   |

Aguarde até oito dias para que esse processo seja concluído. Depois de percorrer essas etapas de publicação, sua oferta de aplicativo Power BI será listada na seção aplicativos do [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power bi.


### <a name="app-certification-process"></a>Processo de certificação do aplicativo

A equipe de integração da Microsoft usa esse processo para validar seu envio de oferta de Power BI aplicativo:

1. Examine documentos legais e links de ajuda.
2. Valide as informações de contato do suporte.
3. Use a URL do instalador para verificar a instalação correta.
4. Examine o aplicativo em busca de malware e outros conteúdos mal-intencionados.
5. Verifique se o conteúdo exibido corresponde à descrição do aplicativo.
6. Verifique se as operações relacionadas ao aplicativo funcionam conforme o esperado no Power BI. A equipe abre relatórios e painéis com dados de exemplo, conecta-se a fontes de dados personalizadas, atualiza dados e assim por diante.

A equipe de certificação fornecerá comentários se encontrar algum problema.  Para obter mais informações sobre os requisitos de aplicativo do Power BI, consulte a [documentação do Power bi app](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Passos seguintes

Recomendamos que você monitore regularmente seu aplicativo no [AppSource Marketplace](https://appsource.microsoft.com).  Você também deve usar o recurso de [informações do vendedor](../../cloud-partner-portal-orig/si-getting-started.md) do [portal do Cloud Partner](https://cloudpartner.azure.com/#insights) para obter informações sobre os clientes do Marketplace e o uso do aplicativo. Por fim, você pode [atualizar sua oferta](./cpp-update-existing-offer.md).
