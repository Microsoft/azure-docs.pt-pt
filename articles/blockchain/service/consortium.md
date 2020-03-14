---
title: Consórcio Azure Blockchain Service
description: Visão geral de como o serviço Azure Blockchain implementa redes blockchain do consórcio.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247621"
---
# <a name="azure-blockchain-service-consortium"></a>Consórcio Azure Blockchain Service

Utilizando o Azure Blockchain Service, pode criar redes privadas de blockchain de consórcio onde cada rede blockchain pode ser limitada a participantes específicos na rede. Apenas os participantes na rede blockchain do consórcio privado podem ver e interagir com a blockchain. As redes de consórcio seleções do Serviço Blockchain Azure podem conter dois tipos de funções participantes membros:

* **Administrador** - Participantes privilegiados que podem tomar ações de gestão do consórcio e podem participar em transações blockchain.

* **Utilizador** - Participantes que não podem tomar qualquer ação de gestão do consórcio, mas podem participar em transações blockchain.

As redes de consórcio podem ser uma mistura de papéis participantes e podem ter um número arbitrário de cada tipo de papel. Deve haver pelo menos um administrador.

O diagrama seguinte mostra uma rede de consórcio com vários participantes:

![Diagrama de rede de consórcio privado](./media/consortium/network-diagram.png)

Com a gestão do consórcio no Azure Blockchain Service, pode gerir os participantes na rede de consórcios. A gestão do consórcio baseia-se no modelo de consenso da rede. No atual lançamento de pré-visualização, o Azure Blockchain Service fornece um modelo de consenso centralizado para a gestão do consórcio. Qualquer participante privilegiado com um papel de administração pode tomar ações de gestão do consórcio, tais como adicionar ou remover participantes de uma rede.

## <a name="roles"></a>Funções

Os participantes num consórcio podem ser indivíduos ou organizações e podem ser atribuídos a uma função de utilizador ou administrador. A tabela que se segue enumera as diferenças de alto nível entre as duas funções:

| Ação | Função de utilizador | Papel de administrador
|--------|:----:|:------------:|
| Criar novo membro | Sim | Sim |
| Convide novos membros | Não | Sim |
| Definir ou alterar a função do participante membro | Não | Sim |
| Alterar o nome do display do membro | Apenas para o próprio membro | Apenas para o próprio membro |
| Remover membros | Apenas para o próprio membro | Sim |
| Participe em transações blockchain | Sim | Sim |

### <a name="user-role"></a>Função de utilizador

Os utilizadores são participantes do consórcio sem capacidadede administrador. Não podem participar na gestão de membros relacionados com o consórcio. Os utilizadores podem alterar o nome do seu membro e podem retirar-se de um consórcio.

### <a name="administrator"></a>Administrador

Um administrador pode gerir membros dentro do consórcio. Um administrador pode convidar membros, remover membros ou atualizar as funções dos membros dentro do consórcio.
Deve haver sempre pelo menos um administrador dentro de um consórcio. O último administrador deve especificar outro participante como administrador antes de deixar um consórcio.

## <a name="managing-members"></a>Gestão de membros

Apenas os administradores podem convidar outros participantes para o consórcio. Os administradores convidam os participantes a usar o seu ID de subscrição Azure.

Uma vez convidados, os participantes podem juntar-se ao consórcio blockchain implantando um novo membro no Azure Blockchain Service. Para visualizar e juntar-se ao consórcio convidado, deve especificar o mesmo ID de subscrição Azure utilizado no convite pelo administrador da rede.

Os administradores podem remover qualquer participante do consórcio, incluindo outros administradores. Os deputados só podem retirar-se de um consórcio.

## <a name="consortium-management-smart-contract"></a>Contrato inteligente de gestão de consórcio

A gestão do consórcio no Azure Blockchain Service é feita através de contratos inteligentes de gestão de consórcios. Os contratos inteligentes são automaticamente implantados nos seus nódosos quando implementa um novo membro blockchain.

O endereço do contrato inteligente de gestão do consórcio raiz pode ser visto no portal Azure. O **endereço RootContract** está na secção de visão geral do membro blockchain.

![Endereço RootContract](./media/consortium/rootcontract-address.png)

Pode interagir com o contrato inteligente de gestão do consórcio utilizando o [módulo powerShell](manage-consortium-powershell.md)de gestão do consórcio, portal Azure, ou diretamente através do contrato inteligente utilizando a conta Ethereum gerada pelo Serviço Blockchain Azure.

## <a name="ethereum-account"></a>Conta Ethereum

Quando um membro é criado, é criada uma chave de conta Ethereum. O Azure Blockchain Service utiliza a chave para criar transações relacionadas com a gestão do consórcio. A chave da conta Ethereum é gerida automaticamente pelo Serviço Blockchain Azure.

A conta membro pode ser vista no portal Azure. A conta do membro está na secção de visão geral do membro blockchain.

![Conta dos membros](./media/consortium/member-account.png)

Pode redefinir a sua conta Ethereum clicando na sua conta de membro e inserindo uma nova senha. Tanto o endereço da conta Ethereum como a palavra-passe serão reiniciados.  

## <a name="next-steps"></a>Passos seguintes

As ações de gestão do consórcio podem ser acedidas através da PowerShell. Para mais informações, consulte [Gerir membros do consórcio no Serviço Blockchain Azure utilizando powerShell](manage-consortium-powershell.md).
