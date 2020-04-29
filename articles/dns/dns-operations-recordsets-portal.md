---
title: Gerir recordes e recordes de DNS com DNS Azure
description: O Azure DNS fornece a capacidade de gerir os recordes e registos do DNS ao hospedar o seu domínio.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 4012b32eb2684126b8dc64b9e86bf35a016e9fba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76936846"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Gerir registos dNS e recordes através do portal Azure

Este artigo mostra-lhe como gerir recordes e registos para a sua zona de DNS utilizando o portal Azure.

É importante entender a diferença entre os recordes de DNS e os registos individuais de DNS. Um conjunto de registos é uma coleção de registos numa zona com o mesmo nome e são do mesmo tipo. Para mais informações, consulte [os recordes e registos do Create DNS utilizando o portal Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Criar um novo recorde e recorde

Para criar um disco estabelecido no portal Azure, consulte os [registos Create DNS utilizando o portal Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Ver um conjunto de recordes

1. No portal Azure, vá à lâmina da **zona DNS.**
2. Procure o conjunto de registos e selecione-o. Isto abre as propriedades do recorde.

    ![Procurar um conjunto de recordes](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adicione um novo recorde a um recorde

Pode adicionar até 20 recordes a qualquer recorde. Um conjunto de registos não pode conter dois registos idênticos. Podem ser criados conjuntos de registos vazios (sem registos zero), mas não aparecem nos servidores de nome SN do Azure. Os conjuntos de registo sem tipo CNAME podem conter um recorde no máximo.

1. No **record set properties** blade para a sua zona DNS, clique no conjunto de registos a que pretende adicionar um recorde.

    ![Selecione um conjunto de registos](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique as propriedades do conjunto de registos preenchendo os campos.

    ![Adicionar um registo](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Clique em **Guardar** na parte superior da lâmina para guardar as definições. Em seguida, feche a lâmina.
4. No canto, verá supor que o recorde está a ser salvo.

    ![Conjunto de recordes de poupança](./media/dns-operations-recordsets-portal/saving150.png)

Depois de o registo ter sido guardado, os valores na lâmina da **zona DNS** refletirão o novo recorde.

## <a name="update-a-record"></a>Atualizar um registo

Quando actualizaum disco num conjunto de registos existente, os campos que pode atualizar dependem do tipo de registo com que está a trabalhar.

1. No **record set properties** blade for your record set, search for the record.
2. Modifique o registo. Quando modificar um disco, pode alterar as definições disponíveis para o registo. No exemplo seguinte, o campo de **endereçoIP** é selecionado e o endereço IP está em fase de modificação.

    ![Modificar um registo](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **Guardar** na parte superior da lâmina para guardar as definições. No canto superior direito, verá a notificação de que o registo foi guardado.

    ![Conjunto de registos guardado](./media/dns-operations-recordsets-portal/saved150.png)

Depois de o recorde ter sido guardado, os valores para o registo estabelecido na lâmina da **zona DNS** refletirão o registo atualizado.

## <a name="remove-a-record-from-a-record-set"></a>Remova um recorde de um conjunto de recordes

Pode utilizar o portal Azure para remover registos de um conjunto de registos. Note que remover o último disco de um conjunto de registos não elimina o conjunto de registos.

1. No **record set properties** blade for your record set, search for the record.
2. Clique no registo que pretende remover. Em seguida, **selecione Remover**.

    ![Remover um disco](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **Guardar** na parte superior da lâmina para guardar as definições.
4. Depois de removido o registo, os valores para o registo na lâmina da **zona DNS** refletirão a remoção.

## <a name="delete-a-record-set"></a><a name="delete"></a>Apagar um conjunto de registos

1. No **conjunto de propriedades do Record** para o seu conjunto de registos, clique em **Eliminar**.

    ![Apagar um conjunto de registos](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Aparece uma mensagem a perguntar se pretende eliminar o conjunto de registos.
3. Verifique se o nome corresponde ao conjunto de registos que pretende eliminar e, em seguida, clique em **Sim**.
4. Na lâmina da **zona DNS,** verifique se o conjunto de registos já não é visível.

## <a name="work-with-ns-and-soa-records"></a>Trabalhar com registos de NS e SOA

Os registos NS e SOA que são automaticamente criados são geridos de forma diferente de outros tipos de discos.

### <a name="modify-soa-records"></a>Modificar registos SOA

Não é possível adicionar ou remover registos do registo SOA criado\@automaticamente no ápice da zona (nome = " "). No entanto, pode modificar qualquer um dos parâmetros dentro do registo SOA (exceto "Host") e o conjunto de discos TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificar os registos de NS no ápice da zona

O registo NS estabelecido no ápice da zona é automaticamente criado com cada zona DNS. Contém os nomes dos servidores de nome SN Azure atribuídos à zona.

Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor DNS. Também pode modificar o TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nome DNS Do DNS do Azure pré-povoados.

Note que isto se aplica apenas ao registo nS estabelecido no ápice da zona. Outros conjuntos de registos de NS na sua zona (como usado para delegar zonas infantis) podem ser modificados sem restrições.

### <a name="delete-soa-or-ns-record-sets"></a>Eliminar conjuntos de recordes SOA ou NS

Não é possível eliminar os conjuntos de registoS SOA e NS no ápice da zona (nome = "\@") que são criados automaticamente quando a zona é criada. São apagados automaticamente quando elimina a zona.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o Azure DNS, consulte a visão geral do [DNS azure](dns-overview.md).
* Para obter mais informações sobre automatização de DNS, consulte [Criar zonas DNS e conjuntos de registos utilizando o .NET SDK](dns-sdk.md).
* Para obter mais informações sobre os registos inversos do DNS, consulte a [visão geral do DNS invertido e suporte em Azure](dns-reverse-dns-overview.md).
* Para obter mais informações sobre os registos de pseudónimos do DNS Azure, consulte a visão geral dos registos do [DNS azure.](dns-alias.md)
