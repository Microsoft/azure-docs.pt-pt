---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971342"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Clique **+Criar um recurso**  >  **Media**  >  **Media Services**.
1. Na secção **Criar uma conta de Serviços de Mídia** introduza os valores necessários.

    | Nome | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta dos Serviços de Comunicação Social. Um nome de conta dos Serviços de Multimédia é composto por letras minúsculas ou números sem espaços, com 3 a 24 carateres de comprimento.|
    |**Subscrição**|Se tiver mais de uma subscrição, selecione uma da lista de subscrições Azure a que tem acesso.|
    |**Grupo de Recursos**|Selecione o recurso novo ou existente. Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Localização**|Selecione a região geográfica que será usada para armazenar os registos de mídia e metadados para a sua conta de Media Services. Esta região será utilizada para processar e transmitir em fluxo a sua multimédia. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente. |
    |**Conta de Armazenamento**|Selecione uma conta de armazenamento para fornecer armazenamento blob do conteúdo dos meios de comunicação a partir da sua conta De Serviços de Media. Pode selecionar uma conta do Storage existente na mesma região geográfica da conta dos Media Services ou pode criar uma nova conta do Storage. É criada uma nova conta do Storage na mesma região. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.<br/><br/>Deve ter uma conta de armazenamento **primário** e pode ter qualquer número de contas de armazenamento **secundária** associadas à sua conta de Serviços de Comunicação Social. Pode utilizar o portal Azure para adicionar contas de armazenamento secundário. Para mais informações, consulte [as contas do Azure Storage com as contas da Azure Media Services](../storage-account-concept.md).<br/><br/>A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Recomendamos vivamente que utilize as contas de armazenamento na mesma localização que a conta dos Serviços de Multimédia para evitar custos de saída de dados e latência adicionais.|
    |**Definições avançadas**| Pode criar uma conta utilizando uma identidade gerida pelo sistema selecionando o botão de rádio gerido pelo **Sistema.**  Fazer esta seleção permitirá utilizar as chaves geridas pelo cliente ou trazer a sua própria chave (BYOK) e Serviços de Mídia para permitir o armazenamento fidedigno.  Para obter mais informações sobre as chaves geridas pelo cliente, consulte [Bring your own key (teclas geridas pelo cliente) com os Media Services](../concept-use-customer-managed-keys-byok.md). Além disso, [as identidades geridas](../concept-managed-identities.md) também serão ativadas.

1. Selecione **Afixar no dashboard** para ver o progresso da implementação da conta.
1. Clique em **Criar** na parte inferior do formulário.
