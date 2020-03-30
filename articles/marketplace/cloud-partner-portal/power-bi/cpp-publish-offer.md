---
title: Publicar oferta de Aplicação Power BI / Mercado Azure
description: Publique uma oferta de Aplicação Power BI no mercado Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: dsindona
ms.openlocfilehash: 89d437fe74d209e0dc04ffc590a1e32426b28732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275871"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicar uma oferta de Aplicação Power BI

O último passo, depois de definir uma oferta no Portal do Parceiro cloud e criar os ativos técnicos associados, é submeter a oferta para publicação. Para iniciar este processo, no painel esquerdo da janela **New Offer,** selecione **Publicar**. Para mais informações, consulte [as ofertas do Publish Azure Marketplace e da AppSource.](../manage-offers/cpp-publish-offer.md)


## <a name="publishing-steps"></a>Etapas de publicação

Estes são os principais passos no processo de publicação:

![Etapas do processo de publicação para oferta de Aplicação Power BI](./media/publishing-process-steps.png)

Este quadro descreve cada passo e fornece o seu tempo estimado de conclusão:

|   Passo de publicação            |   Hora     |   Descrição                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validar pré-requisitos       | 15 minutos     | As informações de oferta e as configurações de oferta são validadas.                            |
| Certificação                | 1-7 dias   | A Equipa de Certificação Power BI analisa a sua oferta. A equipa executa a sua aplicação Power BI através de um teste de verificação manual instalando a aplicação através do URL de instalação fornecido. As validações primárias são realizadas como parte do processo de certificação da aplicação (descrito mais tarde neste documento).         |
| Empacotamento                    | \<1 hora  | Os ativos técnicos da oferta são embalados para uso do cliente.                        |
| Registo de geração de chumbo | \<1 hora  | Os sistemas de chumbo são configurados e implantados.                                      |
| Assinatura de editor            | \-         | Você completa uma revisão final e confirmação antes que a oferta seja em direto. Também terá um link para pré-visualizar a sua oferta. Depois de ficar satisfeito com a aparência da sua pré-visualização, selecione **Go Live** no separador **Status.** Isto envia um pedido à equipa de bordo para listar a sua aplicação no AppSource.    |
| Em direto                         | \<3 horas | A sua oferta está agora publicamente listada ("live") no AppSource, e os clientes podem ver a sua aplicação e implementá-la nas suas subscrições power BI. Também receberá um e-mail de confirmação. Na coluna certa no separador **All offers,** pode ver o estado de todas as suas ofertas. No separador **Status,** pode ver o estado de fluxo de publicação detalhado para a sua oferta. |
|   |   |

Deixe até oito dias para que este processo esteja concluído. Depois de passar por estes passos de publicação, a sua oferta de Aplicação Power BI será listada na secção de aplicações [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI.


### <a name="app-certification-process"></a>Processo de certificação de aplicativos

A equipa de bordo da Microsoft utiliza este processo para validar a sua oferta de oferta power BI:

1. Reveja os documentos legais e ajude links.
2. Validar informações de contacto de suporte.
3. Utilize o URL do instalador para verificar a instalação correta.
4. Scaneie a aplicação para malware e outros conteúdos maliciosos.
5. Verifique se o conteúdo apresentado corresponde à descrição da aplicação.
6. Verifique se as operações relacionadas com aplicações funcionam como esperado no Power BI. A equipa abre relatórios e dashboards com dados de amostra, conecta-se a fontes de dados personalizadas, atualiza dados, e assim por diante.

A Equipa de Certificação fornece feedback se encontrarem algum problema.  Para obter mais informações sobre os requisitos da aplicação Power BI, consulte a documentação da [aplicação Power BI.](https://go.microsoft.com/fwlink/?linkid=2028636)


## <a name="next-steps"></a>Passos seguintes

Recomendamos que monitorize regularmente a sua aplicação no [mercado appSource](https://appsource.microsoft.com).  Também deve utilizar a funcionalidade ["Sellers Insights"](../../cloud-partner-portal-orig/si-getting-started.md) do Portal do [Parceiro cloud](https://cloudpartner.azure.com/#insights) para obter informações sobre os seus clientes de marketplace e o uso de apps. Finalmente, pode [atualizar a sua oferta.](./cpp-update-existing-offer.md)
