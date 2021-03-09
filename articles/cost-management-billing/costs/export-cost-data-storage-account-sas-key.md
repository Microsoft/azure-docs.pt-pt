---
title: Dados de custos de exportação com uma chave SAS de conta de armazenamento Azure
description: Este artigo ajuda os parceiros a criar uma chave SAS e configurar as exportações de Gestão de Custos.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509685"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Dados de custos de exportação com uma chave SAS de conta de armazenamento Azure

As seguintes informações aplicam-se apenas aos parceiros da Microsoft.

Muitas vezes, os parceiros não têm as suas próprias subscrições Azure no inquilino que está associada ao seu próprio Acordo de Parceiros microsoft. Os parceiros com um plano de Acordo de Parceiros da Microsoft que são administradores globais da sua conta de faturação podem exportar e copiar dados de custos numa conta de armazenamento numa conta de armazenamento diferente usando uma chave de serviço de acesso partilhado (SAS). Por outras palavras, uma conta de armazenamento com uma chave SAS permite ao parceiro usar uma conta de armazenamento fora do seu contrato de parceiro para receber informações exportadas. Este artigo ajuda os parceiros a criar uma chave SAS e configurar as exportações de Gestão de Custos.

## <a name="requirements"></a>Requisitos

- Deve ser um parceiro com um Acordo de Parceiro da Microsoft e ter clientes no Plano Azure.
- Deve ser administrador global para a conta de faturação da sua organização parceira.
- Você deve ter acesso a configurar uma conta de armazenamento que está em um inquilino diferente da sua organização parceira. É responsável pela manutenção de permissões e acesso a dados quando os seus dados de exportação para a sua conta de armazenamento.

## <a name="configure-azure-storage-with-a-sas-key"></a>Configure Azure Storage com uma chave SAS

Obtenha uma conta de armazenamento SAS token ou crie uma usando o portal Azure. Para criar no portal Azure, utilize os seguintes passos. Para saber mais sobre as teclas SAS, consulte [Grant acesso limitado a dados com assinaturas de acesso partilhado (SAS).](../../storage/common/storage-sas-overview.md)

1. Navegue para a conta de armazenamento no portal Azure.
    - Se a sua conta tiver acesso a vários inquilinos, troque os diretórios para aceder à conta de armazenamento. Selecione a sua conta no canto superior direito do portal Azure e, em seguida, selecione **Diretórios Switch**.
    - Poderá ter de iniciar seduca no portal Azure com a conta de inquilino correspondente para aceder à conta de armazenamento.
1. No menu esquerdo, selecione **assinatura de acesso partilhado.**  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Screenshot mostrando uma assinatura de acesso compartilhado de armazenamento Azure configurado." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Configure o token com as mesmas definições identificadas na imagem anterior.
    1. Selecione **Blob** para _serviços permitidos_.
    1. Selecione **Serviço,** **Recipiente** e **Objeto** para _tipos de recursos permitidos_.
    1. Selecione **Ler,** **Escrever,** **Eliminar,** **Excluir,** **Adicionar** e **Criar** _permissões permitidas_.
    1. Escolha a validade e as datas. Certifique-se de atualizar o seu token SAS de exportação antes de expirar. Quanto mais tempo configurar antes de expirar, mais tempo a sua exportação passa antes de necessitar de um novo token SAS.
1. Selecione **HTTPS apenas** para _protocolos permitidos._
1. Selecione **Basic** para _o nível de encaminhamento preferido_.
1. Selecione **a tecla1** para _a tecla de assinatura_. Se rodar ou atualizar a chave que é usada para assinar o token SAS, terá de regenerar um novo token SAS para a sua exportação.
1. Selecione **Gerar cadeia de ligação e SAS**.
    O valor **simbólico SAS** mostrado é o símbolo de que precisa quando configura as exportações.

## <a name="create-a-new-export-with-a-sas-token"></a>Criar uma nova exportação com um token SAS

Navegue para **exportações** no âmbito da conta de faturação e crie uma nova exportação utilizando os seguintes passos.

1. Selecione **Criar**.
1. Configure os detalhes da exportação como faria para uma exportação normal. Pode configurar a exportação para utilizar um diretório ou contentor existente ou pode especificar um novo diretório ou contentor e as exportações irão criá-las para si.
1. Ao configurar o armazenamento, selecione **Utilize um token SAS**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Screenshot mostrando a nova exportação onde seleciona o token SAS." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Insira o nome da conta de armazenamento e cole no seu token SAS.
1. Especifique um recipiente ou diretório existente ou identifique novos a serem criados.
1. Selecione **Criar**.

A exportação baseada em símbolos SAS só funciona enquanto o símbolo permanece válido. Repõe o token antes que o atual expire, ou a sua exportação deixará de funcionar. Como o símbolo fornece acesso à sua conta de armazenamento, proteja o token com a maior atenção que qualquer outra informação sensível. É responsável por manter permissões e acesso a dados quando os seus dados de exportação para a sua conta de armazenamento.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Exportações de resolução de problemas utilizando fichas SAS

Seguem-se questões comuns que podem acontecer quando configurar ou utilizar exportações baseadas em símbolos SAS.

- Não se vê a opção chave SAS no portal Azure.
  - Verifique se é um parceiro que tem um Acordo de Parceiro microsoft e que tem permissão de administração global para a conta de faturação. São as únicas pessoas que podem exportar com uma chave SAS.

- Obtém a seguinte mensagem de erro ao tentar configurar a sua exportação:

    **Certifique-se de que o token SAS é válido para o serviço blob, é válido para tipos de recursos de contentores e objetos, e tem permissões: adicione criar a eliminação de escrita de leitura. (Código de erro do serviço de armazenamento: AutorizaçãoResourceTypeMismatch)**

    - Certifique-se de que está a configurar e a gerar corretamente a chave SAS no Azure Storage.

- Não se pode ver a chave SAS completa depois de criar uma exportação.
  - Não ver a chave é comportamento esperado. Após a configuração da exportação SAS, a chave é escondida por razões de segurança.

- Não pode aceder à conta de armazenamento do inquilino onde a exportação está configurada.
  - Espera-se um comportamento. Se a conta de armazenamento estiver em outro inquilino, você precisa navegar para o inquilino primeiro no portal Azure para encontrar a conta de armazenamento.

- A sua exportação falha devido a um erro relacionado com o sasken.
  - A sua exportação funciona apenas enquanto o token SAS permanecer válido. Crie uma nova chave e gere a exportação.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a exportação de dados de Gestão de Custos, consulte [os dados de criação e exportação.](tutorial-export-acm-data.md)
- Para obter informações sobre a exportação de grandes quantidades de dados de utilização, consulte [Recuperar grandes conjuntos de dados com exportações.](ingest-azure-usage-at-scale.md)
