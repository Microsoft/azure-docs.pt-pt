---
title: Proteção de palavra-passe do Azure AD - Azure Active Directory
description: Banir senhas fracas no Active Directory no local ao utilizar a proteção de palavra-passe do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a25dd3a2228f0b1b3ab33db0c9c689d7b2899d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310562"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Impor proteção de palavra-passe do Azure AD para o Windows Server Active Directory

Proteção de palavra-passe do Azure AD é uma funcionalidade que melhora a políticas de palavra-passe de uma organização. Proteção de palavra-passe de implementação no local utiliza os dois as globais e personalizadas banida de palavra-passe listas que são armazenadas no Azure AD. Ele faz o mesmo verificações no local como o Azure AD para alterações com base na cloud.

## <a name="design-principles"></a>Princípios de conceção

Proteção de palavra-passe do Azure AD destina-se com esses princípios em mente:

* Controladores de domínio nunca precisam se comunicar diretamente com a internet.
* Não existem novas portas de rede estão abertas nos controladores de domínio.
* Sem alterações de esquema do Active Directory são necessárias. O software utiliza o Active Directory existente **contentor** e **serviceConnectionPoint** objetos de esquema.
* Nenhum Active Directory domínio ou floresta funcional nível mínimo (DFL/FFL) é necessário.
* O software não criar ou necessitar de contas em domínios do Active Directory por ele protegidos.
* Palavras-passe de texto não encriptado do utilizador não deixam o controlador de domínio durante as operações de validação da palavra-passe ou em qualquer outra altura.
* Implementação incremental é suportada. Mas a política de palavra-passe é imposta apenas onde está instalado o agente de controlador de domínio (DC agente).
* Recomendamos que instale o agente de controlador de domínio em todos os controladores de domínio para garantir a imposição de segurança de proteção de palavra-passe universal.

## <a name="architectural-diagram"></a>Diagrama da arquitetura

É importante compreender a estrutura subjacente e conceitos de função antes de implementar a proteção de palavra-passe do Azure AD no ambiente do Active Directory no local. O diagrama seguinte mostra como os componentes de proteção de palavra-passe funcionam em conjunto:

![Como os componentes de proteção de palavra-passe do Azure AD funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço de Proxy de proteção de palavra-passe do Azure AD é executado em qualquer computador associado a um domínio na floresta do Active Directory atual. Seu objetivo principal é reencaminhar pedidos de transferência da política de palavra-passe de controladores de domínio para o Azure AD. Em seguida, devolve as respostas do Azure AD para o controlador de domínio.
* O filtro de palavras-passe DLL do agente DC recebe pedidos de validação da palavra-passe de utilizador do sistema operativo. Reencaminha-os para o serviço de agente do DC que está a executar localmente no controlador de domínio.
* O serviço de controlador de domínio de agente de proteção de palavra-passe recebe pedidos de validação da palavra-passe do filtro de palavras-passe DLL do agente DC. Ele as processa através da política de palavra-passe (disponível localmente) atual e devolve o resultado: *passar* ou *falhar*.

## <a name="how-password-protection-works"></a>Como funciona a proteção de palavra-passe

Cada instância de serviço de Proxy de proteção de palavra-passe do Azure AD anuncia próprio aos controladores de domínio na floresta através da criação de um **serviceConnectionPoint** objeto no Active Directory.

Cada serviço de agente do controlador de domínio para a proteção de palavra-passe também cria um **serviceConnectionPoint** objeto no Active Directory. Este objeto é utilizado principalmente para geração de relatórios e diagnósticos.

O serviço de agente do controlador de domínio é responsável por iniciar a transferência de uma nova política de palavra-passe do Azure AD. A primeira etapa é localizar um serviço de Proxy de proteção de palavra-passe do Azure AD através da consulta da floresta para o proxy **serviceConnectionPoint** objetos. Quando um serviço de proxy disponível é encontrado, o agente de DC envia um pedido de transferência de política de palavra-passe para o serviço de proxy. O serviço de proxy por sua vez envia o pedido para o Azure AD. O serviço de proxy, em seguida, devolve a resposta para o serviço de agente do controlador de domínio.

Depois do serviço de agente do controlador de domínio recebe uma nova política de palavra-passe do Azure AD, o serviço armazena a política numa pasta dedicada na raiz do domínio *sysvol* partilha de pasta. O serviço de agente do controlador de domínio também monitora esta pasta no caso de políticas mais recentes replicar de outros serviços do agente de controlador de domínio no domínio.

O serviço do agente DC sempre solicita uma nova política durante o arranque do serviço. Depois do serviço do agente DC é iniciado, ele verifica a idade da política atual disponível localmente por hora. Se a política for com mais de uma hora, o agente de controlador de domínio solicita uma nova política do Azure AD, conforme descrito anteriormente. Se a política atual não é mais antiga do que uma hora, o agente de controlador de domínio continua a utilizar essa política.

Sempre que uma política de palavra-passe de proteção de palavra-passe do Azure AD é transferida, essa política é específica para um inquilino. Em outras palavras, as políticas de palavra-passe são sempre uma combinação da lista de banida de palavra-passe global da Microsoft e a lista de banida de palavra-passe personalizada por inquilino.

O agente de controlador de domínio se comunica com o serviço de proxy através de RPC sobre TCP. O serviço de proxy escuta para essas chamadas numa porta RPC dinâmica ou estática, dependendo da configuração.

O agente de controlador de domínio nunca escuta numa porta de rede disponível.

O serviço de proxy nunca chama o serviço de agente do controlador de domínio.

O serviço de proxy é sem monitoração de estado. Ele nunca caches de políticas ou qualquer outro Estado transferido a partir do Azure.

O serviço do agente DC utiliza sempre a política de palavra-passe localmente disponível mais recente para avaliar a palavra-passe de um utilizador. Se nenhuma política de palavra-passe está disponível no controlador de domínio local, a palavra-passe é automaticamente aceites. Quando isso acontece, uma mensagem de evento é registada para avisar o administrador.

Proteção de palavra-passe do Azure AD não é um motor de aplicação de política em tempo real. Pode haver um atraso entre quando uma alteração de configuração de política de palavra-passe é feita no Azure AD e quando alterar a atingir e são aplicadas a todos os controladores de domínio.

## <a name="foresttenant-binding-for-password-protection"></a>Enlace de inquilino/floresta para proteção de palavra-passe

Implementação de proteção de palavra-passe do Azure AD numa floresta do Active Directory requer o registo de nessa floresta com o Azure AD. Cada serviço de proxy que é implementado também tem de ser registado com o Azure AD. Esses registros de floresta e de proxy estão associados a específicas de uma inquilino do Azure AD, o que é identificado implicitamente pelas credenciais que são utilizadas durante o registo.

Floresta do Active Directory e todos os serviços de proxy implementado dentro de uma floresta tem de ser registados com o mesmo inquilino. Não é suportado para ter uma floresta do Active Directory ou de quaisquer serviços de proxy, em que floresta que está a ser registrada no Azure AD diferentes inquilinos. Os sintomas de tipo de implementação configurado incorretamente incluem a impossibilidade de transferir as políticas de palavra-passe.

## <a name="license-requirements"></a>Requisitos de licença

Os benefícios da lista de palavra-passe banidas global aplicam-se a todos os utilizadores do Azure AD.

A lista de banida de palavra-passe personalizada requer licenças do Azure AD básico.

Proteção de palavra-passe do Azure AD para o Windows Server Active Directory requer licenças do Azure AD Premium.

Para obter informações de licenciamento adicionais, consulte [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Transferência

Os dois programas de instalação do agente necessárias para a proteção de palavra-passe do Azure AD estão disponíveis a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passos Seguintes
[Implementar proteção de palavras-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
