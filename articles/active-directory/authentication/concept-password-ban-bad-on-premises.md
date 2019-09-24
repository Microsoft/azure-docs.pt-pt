---
title: Proteção de senha do Azure AD-Azure Active Directory
description: Proibir senhas fracas no local Active Directory usando a proteção por senha do Azure AD
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
ms.openlocfilehash: 013a14505f7ac1382bce369e161fdae834f605fc
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200228"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Impor a proteção de senha do Azure AD ao Windows Server Active Directory

A proteção por senha do Azure AD é um recurso que aprimora as políticas de senha em uma organização. A implantação local da proteção por senha usa as listas de senhas excluídas globais e personalizadas que são armazenadas no Azure AD. Ele faz as mesmas verificações locais que o Azure AD faz para alterações baseadas em nuvem. Essas verificações são executadas durante as alterações de senha e os cenários de redefinição de senha.

## <a name="design-principles"></a>Princípios de conceção

A proteção de senha do Azure AD é projetada com esses princípios em mente:

* Os controladores de domínio nunca precisam se comunicar diretamente com a Internet.
* Nenhuma nova porta de rede é aberta em controladores de domínio.
* Nenhuma alteração de esquema de Active Directory é necessária. O software usa o **contêiner** de Active Directory existente e os objetos de esquema do **serviceConnectionPoint** .
* Nenhum nível funcional de domínio ou floresta de Active Directory mínimo (DFL/FFL) é necessário.
* O software não cria ou exige contas nos domínios de Active Directory que ele protege.
* As senhas de texto não criptografado do usuário nunca deixam o controlador de domínio, seja durante as operações de validação de senha ou a qualquer outro momento.
* O software não depende de outros recursos do Azure AD; por exemplo, a sincronização de hash de senha do Azure AD não está relacionada e não é necessária para que a proteção de senha do Azure AD funcione.
* A implantação incremental tem suporte, no entanto, a política de senha só é imposta quando o agente de controlador de domínio (agente DC) está instalado. Consulte o próximo tópico para obter mais detalhes.

## <a name="incremental-deployment"></a>Implantação incremental

A proteção de senha do Azure AD dá suporte à implantação incremental entre controladores de domínio em um domínio Active Directory, mas é importante entender o que isso realmente significa e quais são as compensações.

O software de agente DC de proteção de senha do Azure AD só pode validar senhas quando ele está instalado em um controlador de domínio e apenas para alterações de senha enviadas para esse controlador de domínio. Não é possível controlar quais controladores de domínio são escolhidos por computadores cliente Windows para processar alterações de senha de usuário. Para garantir o comportamento consistente e a imposição de segurança de proteção por senha universal, o software do agente de DC deve ser instalado em todos os controladores de domínio em um domínio.

Muitas organizações desejarão fazer um teste cuidadoso de proteção de senha do Azure AD em um subconjunto de controladores de domínio antes de fazer uma implantação completa. A proteção de senha do Azure AD oferece suporte à implantação parcial, o IE o software do agente de DC em um determinado DC validará ativamente as senhas mesmo quando outros DCs no domínio não tiverem o software do agente de DC instalado. Implantações parciais desse tipo não são seguras e não são recomendadas além de para fins de teste.

## <a name="architectural-diagram"></a>Diagrama arquitetônico

É importante entender os conceitos subjacentes de design e função antes de implantar a proteção de senha do Azure AD em um ambiente de Active Directory local. O diagrama a seguir mostra como os componentes da proteção por senha funcionam em conjunto:

![Como os componentes de proteção de senha do Azure AD funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço de proxy de proteção de senha do Azure AD é executado em qualquer computador ingressado no domínio na floresta de Active Directory atual. Sua principal finalidade é encaminhar solicitações de download de política de senha de controladores de domínio para o Azure AD. Em seguida, ele retorna as respostas do Azure AD para o controlador de domínio.
* A DLL de filtro de senha do agente de DC recebe solicitações de validação de senha de usuário do sistema operacional. Ele os encaminha para o serviço de agente de DC que está sendo executado localmente no controlador de domínio.
* O serviço de agente de controlador de domínio da proteção por senha recebe solicitações de validação de senha da DLL de filtro de senha do agente de DC. Ele os processa usando a política de senha atual (disponível localmente) e retorna o resultado: *aprovado* ou *falha*.

## <a name="how-password-protection-works"></a>Como funciona a proteção de senha

Cada instância de serviço de proxy de proteção de senha do Azure AD anuncia-se para os controladores de domínio na floresta criando um objeto **serviceConnectionPoint** no Active Directory.

Cada serviço de agente de controlador de domínio para proteção por senha também cria um objeto **serviceConnectionPoint** no Active Directory. Esse objeto é usado principalmente para relatórios e diagnósticos.

O serviço de agente de controlador de domínio é responsável por iniciar o download de uma nova política de senha do Azure AD. A primeira etapa é localizar um serviço de proxy de proteção por senha do Azure AD consultando a floresta em busca de objetos **serviceConnectionPoint** de proxy. Quando um serviço de proxy disponível é encontrado, o agente de DC envia uma solicitação de download de política de senha para o serviço de proxy. O serviço de proxy, por sua vez, envia a solicitação ao Azure AD. Em seguida, o serviço de proxy retorna a resposta ao serviço de agente de controlador de domínio.

Depois que o serviço de agente de controlador de domínio recebe uma nova política de senha do Azure AD, o serviço armazena a política em uma pasta dedicada na raiz de seu compartilhamento de pasta *SYSVOL* de domínio. O serviço de agente de controlador de domínio também monitora essa pasta caso as políticas mais recentes repliquem em de outros serviços de agente de DC no domínio.

O serviço de agente de controlador de domínio sempre solicita uma nova política na inicialização do serviço. Depois que o serviço de agente de controlador de domínio é iniciado, ele verifica a duração da política disponível localmente atual por hora. Se a política for anterior a uma hora, o agente de DC solicitará uma nova política do Azure AD por meio do serviço de proxy, conforme descrito anteriormente. Se a política atual não tiver mais de uma hora, o agente de DC continuará a usar essa política.

Sempre que uma política de senha de proteção de senha do Azure AD é baixada, essa política é específica para um locatário. Em outras palavras, as políticas de senha sempre são uma combinação da lista global banida de senhas da Microsoft e da lista personalizada de senha proibida por locatário.

O agente de DC se comunica com o serviço de proxy via RPC sobre TCP. O serviço de proxy escuta essas chamadas em uma porta RPC dinâmica ou estática, dependendo da configuração.

O agente de DC nunca escuta em uma porta disponível de rede.

O serviço de proxy nunca chama o serviço de agente de controlador de domínio.

O serviço de proxy não tem estado. Ele nunca armazena em cache as políticas ou qualquer outro Estado baixado do Azure.

O serviço de agente de controlador de domínio sempre usa a política de senha mais recente disponível localmente para avaliar a senha de um usuário. Se nenhuma política de senha estiver disponível no controlador de domínio local, a senha será aceita automaticamente. Quando isso acontece, uma mensagem de evento é registrada para avisar o administrador.

A proteção por senha do Azure AD não é um mecanismo de aplicativo de política em tempo real. Pode haver um atraso entre o momento em que uma alteração de configuração de política de senha é feita no Azure AD e quando essa alteração atinge e é imposta em todos os controladores de domínio.

A proteção de senha do Azure AD atua como um suplemento para as políticas de senha de Active Directory existentes, não com uma substituição. Isso inclui qualquer outra DLL de filtro de senha de terceiros que possa ser instalada. Active Directory sempre exige que todos os componentes de validação de senha concordem antes de aceitar uma senha.

## <a name="foresttenant-binding-for-password-protection"></a>Associação de floresta/locatário para proteção de senha

A implantação da proteção de senha do Azure AD em uma floresta Active Directory requer o registro dessa floresta com o Azure AD. Cada serviço de proxy implantado também deve ser registrado com o Azure AD. Esses registros de floresta e proxy são associados a um locatário específico do Azure AD, que é identificado implicitamente pelas credenciais que são usadas durante o registro.

A floresta Active Directory e todos os serviços de proxy implantados em uma floresta devem ser registrados com o mesmo locatário. Não há suporte para ter uma floresta Active Directory ou quaisquer serviços de proxy na floresta que estão sendo registrados em locatários diferentes do Azure AD. Os sintomas de tal implantação mal configurada incluem a incapacidade de baixar políticas de senha.

## <a name="download"></a>Transferência

Os dois instaladores de agente necessários para a proteção de senha do Azure AD estão disponíveis no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Passos seguintes
[Implementar proteção de palavras-passe do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
