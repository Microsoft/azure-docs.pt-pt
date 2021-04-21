---
title: Configurar a Dynamics 365 para o Envolvimento do Cliente & PowerApps oferecem configuração técnica no Microsoft AppSource (Azure Marketplace)
description: Configurar a Dynamics 365 para o Customer Engagement & PowerApps oferecem configuração técnica no Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: da5b6ffd420c2c51e04d3a194ad295089e811db4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820473"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Configurar a Dynamics 365 para o envolvimento do cliente & power apps oferecem configuração técnica

Esta página define os detalhes técnicos utilizados para se ligar à sua oferta. Esta ligação permite-nos oferecer a sua oferta para o cliente final se optarem por adquiri-la.

## <a name="offer-information"></a>Oferecer informações

**O modelo de licença base** determina como os clientes são atribuídos a sua aplicação no CrM Admin Center. Selecione **recurso** por exemplo licenciamento baseado ou **Utilizador** se as licenças forem atribuídas uma por inquilino.

A caixa de verificação de acesso à **loja secure store requer S2S e a** caixa de verificação de acesso à loja segura de CRM permite a configuração do acesso de saída CRM Secure Store ou Server-to-Server (S2S). Esta funcionalidade requer consideração especializada da Equipa Dinâmica 365 durante a fase de certificação. A Microsoft entrará em contacto consigo para completar os passos adicionais para suportar esta funcionalidade.

Deixar **o URL de configuração de aplicação** em branco; é para uso futuro.

## <a name="crm-package"></a>Pacote CRM

Para **URL da sua localização do pacote,** insira o URL de uma conta de Armazenamento Azure Blob que contém o pacote de CRM carregado .zip ficheiro. Inclua uma chave SAS apenas de leitura no URL para que a Microsoft possa recolher o seu pacote para verificação.

> [!IMPORTANT]
> Para evitar um bloco de publicação, certifique-se de que a data de validade no URL do seu armazenamento Blob não expirou. Pode rever a data acedendo à sua política. Recomendamos que o **prazo de expiração** seja pelo menos um mês no futuro.

Selecione o Existe mais de um pacote CRM na minha caixa **de ficheiros de pacote,** se aplicável. Em caso afirmativo, certifique-se de incluir todos os pacotes no seu ficheiro .zip.

Para obter informações detalhadas sobre como construir o seu pacote e atualizar a sua estrutura, consulte [o Passo 3: Criar um pacote AppSource para a sua aplicação.](/powerapps/developer/common-data-service/create-package-app-appsource)

## <a name="crm-package-availability"></a>Disponibilidade de pacotes CRM

Selecione **+ Adicionar região** para especificar as regiões geográficas em que o seu pacote CRM estará disponível para os clientes. A implantação nas seguintes regiões soberanas requer uma autorização e validação especiais durante o processo de certificação: [Alemanha,](../germany/index.yml) [Nuvem do Governo dos EUA](../azure-government/documentation-government-welcome.md)e TIP.

Por predefinição, o **URL de configuração de aplicação** que introduziu acima será utilizado para cada região. Se preferir, pode introduzir um URL de configuração de aplicação separado para uma ou mais regiões específicas.

**Selecione Guardar o rascunho** antes de continuar para o separador seguinte no menu de navegação à esquerda, **Co-vender com a Microsoft**. Para obter informações sobre a configuração da co-venda com a Microsoft (opcional), consulte [o envolvimento do parceiro co-venda](marketplace-co-sell.md). Se não está a preparar a co-venda ou já terminou, continue com **os próximos passos** abaixo.

## <a name="next-steps"></a>Passos seguintes

- [Configure conteúdo suplementar](dynamics-365-customer-engage-supplemental-content.md)
