---
title: Gerir conjuntos de registos DNS e registos DNS do Azure
description: O DNS do Azure fornece a capacidade para gerir os conjuntos de registos DNS e registos ao alojar o seu domínio.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 891adfacde6e46b1d8fe8e2f6b5fb39c90ce27a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296470"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Gerir registos DNS e conjuntos de registos com o portal do Azure

Este artigo mostra-lhe como gerir conjuntos de registos e registos para a sua zona DNS com o portal do Azure.

É importante compreender a diferença entre conjuntos de registos DNS e registos DNS individuais. Um conjunto de registos é uma coleção de registos numa zona com o mesmo nome e o mesmo tipo. Para obter mais informações, consulte [registos com o portal do Azure e conjuntos de registos de DNS criar](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Criar um novo conjunto de registos e o registo

Para criar um conjunto de registos no portal do Azure, veja [registos de DNS de criar com o portal do Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Ver um conjunto de registos

1. No portal do Azure, vá para o **zona DNS** painel.
2. Procure o conjunto de registos e selecioná-lo. Esta ação abre as propriedades do conjunto de registos.

    ![Pesquisar por um conjunto de registos](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adicionar um novo Registro num conjunto de registos

Pode adicionar até 20 registos a qualquer conjunto de registos. Um conjunto de registos não pode conter dois registos idênticos. Conjuntos de registos vazios (com zero registos) podem ser criados, mas não aparecem nos servidores de nome DNS do Azure. Conjuntos de registos do tipo CNAME podem conter, no máximo, um registo.

1. Sobre o **propriedades do conjunto de registos** painel para a sua zona DNS, clique no conjunto de registos que pretende adicionar um registo a.

    ![Selecione um conjunto de registos](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique o registo de definir as propriedades, preenchendo os campos.

    ![Adicione um registo](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Clique em **guardar** na parte superior do painel para guardar as suas definições. Em seguida, feche o painel.
4. No canto, verá que o registo está a guardar.

    ![A guardar conjunto de registos](./media/dns-operations-recordsets-portal/saving150.png)

Após o registo foi guardada, os valores na **zona DNS** painel irá refletir o novo Registro.

## <a name="update-a-record"></a>Atualizar um registo

Quando atualiza um registo de um conjunto de registos existente, os campos que pode atualizar dependem o tipo de registo que está a trabalhar.

1. Sobre o **propriedades do conjunto de registos** painel para o conjunto de registos, procure o registo.
2. Modifique o registo. Quando modificar um registo, pode alterar as definições disponíveis para o registo. No exemplo a seguir, o **endereço IP** campo for selecionado e o endereço IP está no processo a ser modificado.

    ![Modificar um registo](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **guardar** na parte superior do painel para guardar as suas definições. No canto superior direito, verá a notificação de que o registo foi guardado.

    ![Conjunto de registos guardado](./media/dns-operations-recordsets-portal/saved150.png)

Após o registo foi guardado, os valores para o registo definidos na **zona DNS** painel irá refletir o registo atualizado.

## <a name="remove-a-record-from-a-record-set"></a>Remover um registo de um conjunto de registos

Pode utilizar o portal do Azure para remover registos de um conjunto de registos. Tenha em atenção que a remover o último registo de um conjunto de registos não elimina o conjunto de registos.

1. Sobre o **propriedades do conjunto de registos** painel para o conjunto de registos, procure o registo.
2. Clique no registo que pretende remover. Em seguida, selecione **remover**.

    ![Remover um registo](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **guardar** na parte superior do painel para guardar as suas definições.
4. Após o registo tiver sido removido, os valores para o registo no **zona DNS** painel refletirá a remoção.

## <a name="delete"></a>Eliminar um conjunto de registos

1. Sobre o **propriedades do conjunto de registos** painel para o conjunto de registos, clique em **eliminar**.

    ![Eliminar um conjunto de registos](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. É apresentada uma mensagem perguntando se quiser eliminar o conjunto de registos.
3. Certifique-se de que o nome corresponde o conjunto de registos que pretende eliminar e, em seguida, clique em **Sim**.
4. Sobre o **zona DNS** painel, certifique-se de que o conjunto de registos já não está visível.

## <a name="work-with-ns-and-soa-records"></a>Trabalhar com registos NS e SOA

Registos NS e SOA, que são criados automaticamente são gerenciados de forma diferente dos outros tipos de registos.

### <a name="modify-soa-records"></a>Modificar registos SOA

Não é possível adicionar ou remover registos do criado automaticamente SOA conjunto de registos no vértice da zona (nome = "\@"). No entanto, pode modificar qualquer um dos parâmetros no registo SOA (exceto "Host") e o registo de definir o valor de TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificar os registos NS no vértice da zona

O conjunto no vértice da zona de registos NS é criado automaticamente com cada zona DNS. Contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Pode adicionar servidores para este registo NS definidos, para oferecer suporte a domínios co-hospedagem com mais de um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure preenchidos previamente.

Tenha em atenção que isto aplica-se apenas para o conjunto no vértice da zona de registos NS. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser modificados sem restrição.

### <a name="delete-soa-or-ns-record-sets"></a>Eliminar conjuntos de registos SOA ou NS

Não é possível eliminar a SOA e conjuntos de registos NS no vértice da zona (nome = "\@") que são criados automaticamente quando a zona é criada. Eles são eliminados automaticamente ao eliminar a zona.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o DNS do Azure, consulte a [descrição geral do DNS do Azure](dns-overview.md).
* Para obter mais informações sobre como automatizar a DNS, consulte [zonas de criação de DNS e conjuntos de registos com o SDK .NET](dns-sdk.md).
* Para obter mais informações sobre os registos DNS inversos, consulte [descrição geral do DNS inverso e de suporte no Azure](dns-reverse-dns-overview.md).
* Para obter mais informações sobre o Azure DNS alias registos, consulte [DNS do Azure registos descrição geral de alias](dns-alias.md).
