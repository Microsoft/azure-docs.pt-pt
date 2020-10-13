---
title: Gestão de chumbo do mercado comercial da Microsoft
description: Saiba como gerar e receber leads de clientes a partir das suas ofertas microsoft AppSource e Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 5627d6afab22defebeebd3f04695ef95390f1163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653320"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>O cliente lidera a partir da sua oferta de mercado comercial

Os leads são clientes interessados ou a implementar as suas ofertas a partir do [Microsoft AppSource](https://appsource.microsoft.com) e [do Azure Marketplace.](https://azuremarketplace.microsoft.com) Pode receber os leads do cliente após a sua oferta ser publicada no mercado comercial. Este artigo explica os seguintes conceitos de gestão de chumbo:

* A forma como a sua oferta de mercado comercial gera o cliente leva a garantir que não perde oportunidades de negócio. 
* Como ligar o seu sistema de gestão de relacionamento com o cliente (CRM) à sua oferta para que possa gerir os seus leads numa localização central.
* Os dados de chumbo que lhe enviamos para que possa acompanhar os clientes que o contactaram.

## <a name="generate-customer-leads"></a>Gere a 8.000 que os

Aqui estão os lugares onde uma pista é gerada:

- Um cliente consente em partilhar as suas informações depois de selecionar **contacte-me** a partir do mercado comercial. Esta pista é uma vantagem inicial de *juros.* Partilhamos consigo informações sobre o cliente que manifestou interesse em obter o seu produto. O chumbo é o topo do funil de aquisição.

    ![Dinâmica 365 Contacte-me](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Um cliente seleciona **Get It Now** (ou seleciona **Criar** no [portal Azure)](https://portal.azure.com/)para obter a sua oferta. Esta pista é uma pista *ativa.* Partilhamos consigo informações sobre o cliente que começou a implementar o seu produto.

    ![SQL Get It Now botão](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Botão criar servidor do Windows](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Um cliente seleciona **Test Drive** ou **Teste Gratuito** para experimentar a sua oferta. Test drives ou testes gratuitos são oportunidades aceleradas para partilhar o seu negócio instantaneamente com potenciais clientes sem quaisquer barreiras de entrada.

    ![Botão de unidade de teste Dinâmico 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Botão de ensaio gratuito Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Ligue-se ao seu sistema CRM

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Compreender dados de chumbo

Cada chumbo que recebe durante o processo de aquisição do cliente tem dados em áreas específicas. O primeiro campo a ter em conta é o `LeadSource` campo, que segue este formato: **Oferta de Ação de Fonte**  |  **Offer**.

**Fontes**: O valor deste campo é povoado com base no mercado que gerou o chumbo. Os valores possíveis `"AzureMarketplace"` `"AzurePortal"` são, `"AppSource (SPZA)"` e.

**Ações**: O valor deste campo é povoado com base na ação que o cliente tomou no mercado que gerou o chumbo.

Os valores possíveis são:

- **"INS"**: Significa *instalação.* Esta ação está no Azure Marketplace ou AppSource quando um cliente adquire o seu produto.
- **"PLT"**: Significa julgamento liderado por *parceiros.* Esta ação está no AppSource quando um cliente seleciona a opção **Contacte-me.**
- **"DNC"**: Significa *não contactar*. Esta ação está no AppSource quando um parceiro que foi listado na sua página de aplicações é solicitado para ser contactado. Partilhamos uma notificação de que este cliente estava listado na sua aplicação, mas não precisa de ser contactado.
- **"Criar"** esta ação está apenas dentro do portal Azure e é gerada quando um cliente compra a sua oferta à sua conta.
- **"StartTestDrive"**: Esta ação destina-se apenas à opção **Test Drive** e é gerada quando um cliente inicia o seu test drive.

**Ofertas**: Pode ter várias ofertas no mercado comercial. O valor deste campo é povoado com base na oferta que gerou o chumbo. O ID da editora e o ID de oferta são ambos enviados neste campo e são valores que forneceu quando publicou a oferta ao mercado.

Os seguintes exemplos mostram valores no formato `publisherid.offerid` esperado: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informação do cliente

A informação do cliente é enviada através de vários campos. O exemplo a seguir mostra a informação do cliente que está contida numa pista:

- Primeiro Nome: João
- Último Nome: Smith
- E-mail: jsmith \@ microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Denominação: CTO

>[!NOTE]
>Nem todos os dados do exemplo anterior estão sempre disponíveis para cada chumbo. Como obtém pistas de vários passos, como mencionado na secção "Gerar leads de cliente", a melhor maneira de lidar com os leads é des duplicar os registos e personalizar os seguimentos. Desta forma, cada cliente recebe uma mensagem apropriada, e cria uma relação única.

## <a name="best-practices-for-lead-management"></a>Melhores práticas para gestão de chumbo

Aqui ficam algumas recomendações para conduzir condutores durante o seu ciclo de vendas:

- **Processo**: Defina um processo de venda claro, com marcos, análises e clara propriedade da equipa.
- **Qualificação**: Definir pré-requisitos, que indicam se uma vantagem foi totalmente qualificada. Certifique-se de que os representantes de vendas ou marketing se qualificam com cuidado antes de os levar em todo o processo de venda.
- **Seguimento**: Não se esqueça de acompanhar dentro de 24 horas. Obterá o chumbo na sua CRM de eleição imediatamente após o cliente implementar uma unidade de teste; enviar-lhes por e-mail dentro enquanto ainda estão quentes. Solicite agendar uma chamada para entender melhor se o seu produto é uma boa solução para o seu problema. Espere que a transação típica exija inúmeras chamadas de acompanhamento.
- **Nutrir:** Nutrir as suas pistas para levá-lo a caminho de uma margem de lucro mais elevada. Faça o check-in, mas não os bombardeie. Recomendamos que envie um e-mail com, pelo menos, algumas vezes antes de os fechar; não desista depois da primeira tentativa. Lembre-se, estes clientes diretamente se envolveram com o seu produto e passaram algum tempo em um teste gratuito; são grandes perspetivas.

Após a configuração técnica, incorpore estes leads na sua estratégia atual de vendas e marketing e processos operacionais. Estamos interessados em compreender melhor o seu processo global de vendas e queremos trabalhar em estreita colaboração consigo para fornecer pistas de alta qualidade e dados suficientes para o tornar bem sucedido. Congratulamo-nos com o seu feedback sobre como podemos otimizar e melhorar os leads que lhe enviamos com dados adicionais para ajudar a tornar estes clientes bem sucedidos. Informe-nos se estiver interessado em [fornecer feedback](mailto:AzureMarketOnboard@microsoft.com) e sugestões para que a sua equipa de vendas tenha mais sucesso com os líderes comerciais do mercado.

## <a name="next-steps"></a>Passos seguintes

- [FaQ de gestão de chumbo e resolução de problemas](../lead-management-faq.md)