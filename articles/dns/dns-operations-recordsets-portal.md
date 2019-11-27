---
title: Gerenciar registros e conjuntos de registros DNS com o DNS do Azure
description: O DNS do Azure fornece a capacidade de gerenciar registros e conjuntos de registros DNS ao hospedar seu domínio.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: allensu
ms.openlocfilehash: 6d0b917c72b1ebb6f1156df269908fb9af3657ed
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211788"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Gerenciar registros DNS e conjuntos de registros usando o portal do Azure

Este artigo mostra como gerenciar os registros e conjuntos de registros de sua zona DNS usando o portal do Azure.

É importante entender a diferença entre os conjuntos de registros DNS e os registros DNS individuais. Um conjunto de registros é uma coleção de registros em uma zona que tem o mesmo nome e que são do mesmo tipo. Para obter mais informações, consulte [criar registros e conjuntos de registros DNS usando o portal do Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Criar um novo conjunto de registros e registro

Para criar um conjunto de registros no portal do Azure, consulte [criar registros DNS usando o portal do Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Exibir um conjunto de registros

1. Na portal do Azure, vá para a folha **zona DNS** .
2. Pesquise o conjunto de registros e selecione-o. Isso abre as propriedades do conjunto de registros.

    ![Pesquisar um conjunto de registros](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adicionar um novo registro a um conjunto de registros

Você pode adicionar até 20 registros a qualquer conjunto de registros. Um conjunto de registros não pode conter dois registros idênticos. Conjuntos de registros vazios (com zero registros) podem ser criados, mas não aparecem nos servidores de nome DNS do Azure. Os conjuntos de registros do tipo CNAME podem conter um registro no máximo.

1. Na folha de **Propriedades do conjunto de registros** da zona DNS, clique no conjunto de registros ao qual você deseja adicionar um registro.

    ![Selecionar um conjunto de registros](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique as propriedades do conjunto de registros preenchendo os campos.

    ![Adicionar um registro](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Clique em **salvar** na parte superior da folha para salvar as configurações. Em seguida, feche a folha.
4. No canto, você verá que o registro está sendo salvo.

    ![Salvando conjunto de registros](./media/dns-operations-recordsets-portal/saving150.png)

Depois que o registro tiver sido salvo, os valores na folha **zona DNS** refletirão o novo registro.

## <a name="update-a-record"></a>Atualizar um registro

Quando você atualiza um registro em um conjunto de registros existente, os campos que você pode atualizar dependem do tipo de registro com o qual você está trabalhando.

1. Na folha de **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.
2. Modifique o registro. Ao modificar um registro, você pode alterar as configurações disponíveis para o registro. No exemplo a seguir, o campo **endereço IP** está selecionado e o endereço IP está em processo de modificação.

    ![Modificar um registro](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **salvar** na parte superior da folha para salvar as configurações. No canto superior direito, você verá a notificação de que o registro foi salvo.

    ![Conjunto de registros salvo](./media/dns-operations-recordsets-portal/saved150.png)

Depois que o registro tiver sido salvo, os valores para o conjunto de registros na folha **zona DNS** refletirão o registro atualizado.

## <a name="remove-a-record-from-a-record-set"></a>Remover um registro de um conjunto de registros

Você pode usar o portal do Azure para remover registros de um conjunto de registros. Observe que a remoção do último registro de um conjunto de registros não exclui o conjunto de registros.

1. Na folha de **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.
2. Clique no registro que você deseja remover. Em seguida, selecione **remover**.

    ![Remover um registro](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **salvar** na parte superior da folha para salvar as configurações.
4. Depois que o registro tiver sido removido, os valores para o registro na folha **zona DNS** refletirão a remoção.

## <a name="delete"></a>Excluir um conjunto de registros

1. Na folha de **Propriedades do conjunto de registros** do conjunto de registros, clique em **excluir**.

    ![Excluir um conjunto de registros](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Será exibida uma mensagem perguntando se você deseja excluir o conjunto de registros.
3. Verifique se o nome corresponde ao conjunto de registros que você deseja excluir e clique em **Sim**.
4. Na folha **zona DNS** , verifique se o conjunto de registros não está mais visível.

## <a name="work-with-ns-and-soa-records"></a>Trabalhar com registros NS e SOA

Os registros NS e SOA criados automaticamente são gerenciados de forma diferente de outros tipos de registro.

### <a name="modify-soa-records"></a>Modificar registros SOA

Você não pode adicionar ou remover registros do conjunto de registros SOA criado automaticamente no Apex da zona (nome = "\@"). No entanto, você pode modificar qualquer um dos parâmetros no registro SOA (exceto "host") e o TTL do conjunto de registros.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificar registros NS no Apex da zona

O conjunto de registros NS no Apex da zona é criado automaticamente com cada zona DNS. Ele contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Você pode adicionar servidores de nomes adicionais a esse conjunto de registros NS para dar suporte a domínios de hospedagem com mais de um provedor de DNS. Você também pode modificar o TTL e os metadados para este conjunto de registros. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure populados previamente.

Observe que isso se aplica somente ao conjunto de registros NS no Apex da zona. Outros conjuntos de registros NS em sua zona (como usados para delegar zonas filhas) podem ser modificados sem restrição.

### <a name="delete-soa-or-ns-record-sets"></a>Excluir conjuntos de registros SOA ou NS

Você não pode excluir os conjuntos de registros SOA e NS no Apex da zona (Name = "\@") que são criados automaticamente quando a zona é criada. Eles são excluídos automaticamente quando você exclui a zona.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o DNS do Azure, consulte a [visão geral do DNS do Azure](dns-overview.md).
* Para obter mais informações sobre como automatizar o DNS, consulte [criando zonas DNS e conjuntos de registros usando o SDK do .net](dns-sdk.md).
* Para obter mais informações sobre registros DNS reversos, consulte [visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md).
* Para obter mais informações sobre os registros de alias do DNS do Azure, consulte [visão geral dos registros de alias do DNS do Azure](dns-alias.md).
