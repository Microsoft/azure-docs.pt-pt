---
title: Gerir recordes e recordes de DNS com Azure DNS
description: O Azure DNS fornece a capacidade de gerir conjuntos e registos de discos DNS ao hospedar o seu domínio.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 111d94db1cbec658daabfb1a4c38c8160d6f50b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84696836"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Gerir registos de DNS e recordes utilizando o portal Azure

Este artigo mostra-lhe como gerir conjuntos de registos e registos para a sua zona de DNS utilizando o portal Azure.

É importante entender a diferença entre os recordes de DNS e os registos individuais de DNS. Um conjunto de recordes é uma coleção de discos numa zona que tem o mesmo nome e são do mesmo tipo. Para obter mais informações, consulte [Configurar discos e registos DNS utilizando o portal Azure](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Crie um novo recorde e recorde

Para criar um recorde estabelecido no portal Azure, consulte [os registos Create DNS utilizando o portal Azure](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Ver um conjunto de recordes

1. No portal Azure, vá à lâmina da **zona DONS.**
2. Procure o recorde definido e selecione-o. Isto abre as propriedades recorde.

    ![Procure um recorde](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adicione um novo recorde a um recorde

Pode somar 20 recordes a qualquer recorde estabelecido. Um recorde não pode conter dois registos idênticos. Podem ser criados conjuntos de registos vazios (com zero registos), mas não aparecem nos servidores de nomes Azure DNS. Conjuntos de recordes de tipo CNAME podem conter um recorde no máximo.

1. Na lâmina **de fixação de propriedades para** a sua zona DNS, clique no conjunto de registos a que pretende adicionar um recorde.

    ![Selecione um conjunto de recordes](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique as propriedades de recordes preenchendo os campos.

    ![Adicionar um registo](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Clique em **Guardar** na parte superior da lâmina para guardar as suas definições. Em seguida, feche a lâmina.
4. No canto, verá que o recorde está a poupar.

    ![Conjunto de recordes de poupança](./media/dns-operations-recordsets-portal/saving150.png)

Depois de guardado o registo, os valores na lâmina da **zona DNS** refletirão o novo registo.

## <a name="update-a-record"></a>Atualizar um registo

Quando atualiza um registo num conjunto de registos existente, os campos que pode atualizar dependem do tipo de registo com que está a trabalhar.

1. No **Record definir** a lâmina de propriedades para o seu recorde, procure o recorde.
2. Modifique o registo. Quando modificar um registo, pode alterar as definições disponíveis para o registo. No exemplo seguinte, o campo **de endereços IP** é selecionado e o endereço IP está em processo de modificação.

    ![Modificar um registo](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **Guardar** na parte superior da lâmina para guardar as suas definições. No canto superior direito, verá a notificação de que o registo foi guardado.

    ![Conjunto de recordes guardado](./media/dns-operations-recordsets-portal/saved150.png)

Depois de ter sido guardado o registo, os valores para o recorde estabelecido na lâmina da **zona DENS** refletirão o registo atualizado.

## <a name="remove-a-record-from-a-record-set"></a>Remova um recorde de um conjunto de recordes

Pode utilizar o portal Azure para remover registos de um conjunto de registos. Note que remover o último recorde de um conjunto de discos não apaga o recorde estabelecido.

1. No **Record definir** a lâmina de propriedades para o seu recorde, procure o recorde.
2. Clique no registo que pretende remover. Em seguida, **selecione Remover**.

    ![Remova um registo](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **Guardar** na parte superior da lâmina para guardar as suas definições.
4. Depois de removido o registo, os valores para o registo na lâmina da **zona DNS** refletirão a remoção.

## <a name="delete-a-record-set"></a><a name="delete"></a>Apagar um conjunto de recordes

1. Na lâmina **de fixação de propriedades para** o seu conjunto de registos, clique em **Eliminar**.

    ![Apagar um conjunto de recordes](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Aparece uma mensagem a perguntar se pretende eliminar o recorde definido.
3. Verifique se o nome corresponde ao conjunto de registos que pretende apagar e, em seguida, clique em **Sim**.
4. Na lâmina da **zona DNS,** verifique se o conjunto de registos já não está visível.

## <a name="work-with-ns-and-soa-records"></a>Trabalhar com registos NS e SOA

Os registos NS e SOA que são criados automaticamente são geridos de forma diferente de outros tipos de registo.

### <a name="modify-soa-records"></a>Modificar os registos SOA

Não é possível adicionar ou remover registos do registo SOA automaticamente criado no ápice da zona (nome = " \@ "). No entanto, pode modificar qualquer um dos parâmetros dentro do registo SOA (exceto "Host") e o recorde definido TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificar os registos NS no ápice da zona

O recorde NS estabelecido no ápice da zona é automaticamente criado com cada zona DE DNS. Contém os nomes dos servidores de nomeS DNS Azure atribuídos à zona.

Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor de DNS. Também pode modificar o TTL e metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomeS DNS pré-povoados.

Note que isto se aplica apenas ao recorde NS estabelecido no ápice da zona. Outros conjuntos de registos NS na sua zona (como usado para delegar zonas infantis) podem ser modificados sem restrições.

### <a name="delete-soa-or-ns-record-sets"></a>Eliminar conjuntos de recordes SOA ou NS

Não é possível eliminar os conjuntos de registos SOA e NS no ápice da zona (nome = " \@ ") que são criados automaticamente quando a zona é criada. São apagados automaticamente quando elimina a zona.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o Azure DNS, consulte a visão geral do [Azure DNS](dns-overview.md).
* Para obter mais informações sobre a automatização de DNS, consulte [criar zonas DNS e gravar conjuntos utilizando o .NET SDK](dns-sdk.md).
* Para obter mais informações sobre registos DNS invertidos, consulte [a visão geral do DNS invertido e o suporte em Azure](dns-reverse-dns-overview.md).
* Para obter mais informações sobre os registos de pseudónimos do Azure DNS, consulte [a visão geral dos registos do Azure DNS](dns-alias.md).
