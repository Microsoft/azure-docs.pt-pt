---
title: Dynamics 365 para o separador de informações técnicas do Customer Engagement - Azure Marketplace | Documentos da Microsoft
description: Como especificar as informações técnicas para um de Dynamics 365 para a aplicação de envolvimento do cliente no Marketplace do AppSource.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
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
ms.date: 03/05/2019
ms.author: pbutlerm
ms.openlocfilehash: 1dd488c2eb419b5e210a48d7a94f7d0bb423a2b1
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731476"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 para o separador de informações de técnicas de envolvimento do cliente

O **informações técnicas** separador da **nova oferta** página permite-lhe especificar informações detalhadas sobre o Dynamics 365 para a aplicação de envolvimento do cliente, incluindo recursos de logótipo de pacote e de marketing de CRM.  Este separador é dividido em quatro seções: **Informace aplikace**, **CRM pacote**, **disponibilidade do pacote CRM**, e **artefactos de Marketing**. Um anexado asterisco (*) no nome de um campo indica que é necessário. 


## <a name="application-info-section"></a>Secção de informações da aplicação

Deve fornecer detalhes sobre a sua aplicação do Dynamics 365 nesta secção.

![Secção de informações da aplicação do separador de informações técnicas](./media/dynce-technical-info-tab1.png)

A tabela seguinte descreve estes campos.

|      Campo                    |    Descrição                  |
|    ---------                  |  ---------------                |
|   Modelo de licença de base          |  Modelo de licença determina a forma como os clientes são atribuídos a sua aplicação no Centro de administração do Dynamics 365. **Resource** licenciamento é baseada na instância, enquanto **utilizador** licenças são atribuídas uma por inquilino.  |
|  Saída de S2S & CRM Secure Store acesso |  Permite a configuração de CRM Secure Store ou o acesso de saída do servidor para servidor (S2S). *Esta funcionalidade requer consideração especializada da equipe de do Dynamics 365 durante a fase de certificação.* Microsoft irá contactá-lo para concluir os passos adicionais para suportar esta funcionalidade.  |
| Subscrever eventos de ciclo de vida CRM | Integração com eventos de ciclo de vida do Dynamics 365 requer que forneça um serviço dedicado que está registado através de um contrato especial com a Microsoft. *Esta funcionalidade requer consideração especializada da equipe de do Dynamics 365 durante a fase de certificação.* Será contactado para concluir os passos adicionais para suportar esta capacidade.  |
| Url de configuração de aplicação | URL da página web que permite ao usuário configurar a aplicação |
| Produtos aplicáveis do Dynamics 365  | Selecione os produtos do Dynamics 365 que esta oferta aplica-se a. Esta oferta serão apresentados no produtos selecionados no AppSource.  |
| Apenas a alteração de marketing         | Definir esta opção para Sim indica que apenas marketing/descritivo alterações foram efetuadas para a oferta existente.  Essas alterações permitem a oferta para ignorar a certificação e aprovisionamento de fases.  |
|  |  |


## <a name="crm-package-section"></a>Secção de pacote de CRM

Deve fornecer detalhes sobre o ficheiro de pacote do AppSource nesta secção.  Estas informações serão utilizadas pelas equipes de validação e certificação de Dynamics 365.

![Secção de pacote de CRM do separador de informações técnicas](./media/dynce-technical-info-tab2.png)

A tabela seguinte descreve estes campos.

|      Campo                    |    Descrição                  |
|    ---------                  |  ---------------                |
|  Nome de ficheiro do pacote     |  Nome de ficheiro do pacote (. zip).  Este nome é *não* pública e será usado internamente pela equipe de certificação do Dynamics 365.  |
|  Url                          |  URL de uma conta de armazenamento do Azure que contém o ficheiro de pacote. Este URL deve incluir uma chave SAS só de leitura para permitir que a nossa equipa de retirada do seu pacote para a verificação.  |
| Mais do que um pacote de crm     | Selecione Sim apenas se estiver dando suporte a várias versões do crm com diferentes pacotes.  Cada versão terão um tht correspondente do ficheiro de pacote que tem de criar individualmente.  |
| Recurso de caso de cenário e a utilização   | Permite o carregamento de um documento de especificação funcional para a sua aplicação, para utilização pela equipe de validação do Dynamics 365.  O formato preferencial para essa especificação é o [modelo de cenário de utilizador de E2E](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>Secção de disponibilidade do pacote de CRM

Nesta secção, selecione as regiões geográficas a aplicação ficará disponível para clientes.  A implementar as seguintes regiões de soberania *necessitam de permissões especiais e validação* durante o processo de certificação: [Alemanha](https://docs.microsoft.com/azure/germany/), [Cloud do Governo dos Estados Unidos](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)e a sugestão.


## <a name="marketing-artifacts-section"></a>Secção de artefactos de marketing

Nesta secção exige que carregue um logótipo da aplicação que será utilizado para representar o pacote no Marketplace do AppSource.  A imagem do logótipo tem de estar no formato PNG e possível de pixels de tamanho 255 x 115.


## <a name="next-steps"></a>Passos Seguintes

Recomendamos que oferecem uma demonstração do seu aplicativo, concluindo o [separador de Test-Drive](./cpp-testdrive-tab.md)
