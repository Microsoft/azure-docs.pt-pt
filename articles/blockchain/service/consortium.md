---
title: Consórcio Azure Blockchain Service
description: Visão geral de como o serviço Azure Blockchain implementa redes de blockchain de consórcio.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84712535"
---
# <a name="azure-blockchain-service-consortium"></a>Consórcio Azure Blockchain Service

Utilizando o Azure Blockchain Service, pode criar redes privadas de blockchain de consórcios onde cada rede blockchain pode ser limitada a participantes específicos na rede. Apenas os participantes na rede privada blockchain do consórcio podem ver e interagir com a blockchain. As redes de consórcio no Azure Blockchain Service podem conter dois tipos de funções de participante:

* **Administrador** - Participantes privilegiados que podem tomar ações de gestão de consórcios e podem participar em transações blockchain.

* **Utilizador** - Participantes que não podem tomar qualquer ação de gestão do consórcio mas podem participar em transações blockchain.

As redes de consórcio podem ser uma mistura de papéis participantes e podem ter um número arbitrário de cada tipo de função. Deve haver pelo menos um administrador.

O diagrama a seguir mostra uma rede de consórcio com vários participantes:

![Diagrama de rede de consórcio privado](./media/consortium/network-diagram.png)

Com a gestão de consórcios no Azure Blockchain Service, pode gerir os participantes na rede de consórcios. A gestão do consórcio baseia-se no modelo de consenso da rede. No atual lançamento de pré-visualização, o Azure Blockchain Service fornece um modelo de consenso centralizado para a gestão do consórcio. Qualquer participante privilegiado com um papel de administração pode assumir ações de gestão de consórcios, tais como adicionar ou remover participantes de uma rede.

## <a name="roles"></a>Funções

Os participantes num consórcio podem ser indivíduos ou organizações e podem ser atribuídos a um papel de utilizador ou administrador. A tabela a seguir enumera as diferenças de alto nível entre as duas funções:

| Ação | Função de utilizador | Função de administrador
|--------|:----:|:------------:|
| Criar novo membro | Yes | Yes |
| Convidar novos membros | No | Yes |
| Definir ou alterar o papel de participante do membro | No | Yes |
| Alterar nome de exibição de membro | Apenas para o próprio membro | Apenas para o próprio membro |
| Remover membros | Apenas para o próprio membro | Yes |
| Participar em transações blockchain | Yes | Yes |

### <a name="user-role"></a>Função de utilizador

Os utilizadores são participantes do consórcio sem capacidade de administrador. Não podem participar na gestão de membros relacionados com o consórcio. Os utilizadores podem alterar o nome de exibição dos seus membros e podem retirar-se de um consórcio.

### <a name="administrator"></a>Administrador

Um administrador pode gerir membros dentro do consórcio. Um administrador pode convidar membros, remover membros ou atualizar funções de membros dentro do consórcio.
Deve haver sempre pelo menos um administrador dentro de um consórcio. O último administrador deve especificar outro participante como administrador antes de deixar um consórcio.

## <a name="managing-members"></a>Membros de gestão

Apenas os administradores podem convidar outros participantes para o consórcio. Os administradores convidam os participantes a utilizar o seu ID de subscrição Azure.

Uma vez convidados, os participantes podem aderir ao consórcio blockchain implantando um novo membro no Azure Blockchain Service. Para visualizar e aderir ao consórcio convidado, deve especificar o mesmo ID de assinatura Azure utilizado no convite pelo administrador da rede.

Os administradores podem remover qualquer participante do consórcio, incluindo outros administradores. Os deputados só podem retirar-se de um consórcio.

## <a name="consortium-management-smart-contract"></a>Contrato inteligente de gestão de consórcio

A gestão do consórcio no Azure Blockchain Service é feita através de contratos inteligentes de gestão de consórcios. Os contratos inteligentes são automaticamente implantados nos seus nós quando implementa um novo membro blockchain.

O endereço do contrato inteligente de gestão de consórcios de raiz pode ser visto no portal Azure. O **endereço RootContract** está na secção de visão geral do membro blockchain.

![Endereço RootContract](./media/consortium/rootcontract-address.png)

Pode interagir com o contrato inteligente de gestão do consórcio utilizando o [módulo PowerShell](manage-consortium-powershell.md)de gestão do consórcio, portal Azure, ou diretamente através do contrato inteligente utilizando a conta Ethereum gerada pelo Serviço Azure Blockchain.

## <a name="ethereum-account"></a>Conta Ethereum

Quando um membro é criado, uma chave de conta Ethereum é criada. O Azure Blockchain Service utiliza a chave para criar transações relacionadas com a gestão do consórcio. A chave da conta Ethereum é gerida automaticamente pelo Azure Blockchain Service.

A conta de membro pode ser vista no portal Azure. A conta do membro está na secção de visão geral do membro blockchain.

![Conta de membro](./media/consortium/member-account.png)

Pode redefinir a sua conta Ethereum clicando na sua conta de membro e introduzindo uma nova palavra-passe. Tanto o endereço da conta Ethereum como a palavra-passe serão reiniciados.  

## <a name="next-steps"></a>Passos seguintes

As ações de gestão do consórcio podem ser acedidas através da PowerShell. Para obter mais informações, consulte [Gerir membros do consórcio no Serviço Azure Blockchain utilizando o PowerShell](manage-consortium-powershell.md).
