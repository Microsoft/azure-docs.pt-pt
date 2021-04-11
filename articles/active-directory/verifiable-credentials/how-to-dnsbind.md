---
title: Ligue o seu Domínio ao seu Identificador Descentralizado (DID) (pré-visualização)
description: Aprender a DNS Bind?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: be7db16a8e3a827d08c0db637961bf004af1d621
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170241"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Ligue o seu Domínio ao seu Identificador Descentralizado (DID)

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Neste artigo:
> [!div class="checklist"]
> * Por que precisamos ligar o nosso DID ao nosso domínio?
> * Como ligamos DIDs e domínios?
> * Como funciona o processo de ligação de domínio?
> * Como funciona a lógica de domínio verificado/não verificado?

## <a name="prerequisites"></a>Pré-requisitos

Para ligar o seu DID ao seu domínio, precisa de ter concluído o seguinte.

- Complete o Conjunto de [Início e](get-started-verifiable-credentials.md) [tutoriais subsequentes](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Por que precisamos ligar o nosso DID ao nosso domínio?

Um DID começa como um identificador que não está ancorado aos sistemas existentes. Um DID é útil porque um utilizador ou organização pode possuí-lo e controlá-lo. Se uma entidade que interage com a organização não sabe "a quem" pertence o DID, então o DID não é tão útil.

Ligar um DID a um domínio resolve o problema de confiança inicial, permitindo que qualquer entidade verifique criptograficamente a relação entre um DID e um Domínio.

## <a name="how-do-we-link-dids-and-domains"></a>Como ligamos DIDs e domínios?

Fazemos uma ligação entre um domínio e um DID implementando um padrão aberto escrito pela Fundação de Identidade Descentralizada chamada [configuração DID bem conhecida.](https://identity.foundation/.well-known/resources/did-configuration/) O serviço de credenciais verificáveis no Azure Ative Directory (Azure AD) ajuda a sua organização a fazer a ligação entre o DID e o domínio, incluindo as informações de domínio que forneceu no seu DID, e gerando o conhecido ficheiro config:

1. A Azure AD utiliza as informações de domínio que fornece durante a configuração da organização para escrever um Ponto de Finalização de Serviço dentro do Documento DID. Todas as partes que interagem com o seu DID podem ver o domínio com o qual o seu DID proclama estar associado.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. O serviço de credencial verificável em Azure AD gera um recurso de configuração bem conhecido que pode hospedar no seu domínio. O ficheiro de configuração inclui uma credencial verificável auto-emitida de credencialType 'DomainLinkageCredential' assinada com o seu DID que tem uma origem do seu domínio. Aqui está um exemplo do config doc que é armazenado no url de domínio raiz.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Depois de ter o conhecido ficheiro de configuração, tem de disponibilizar o ficheiro utilizando o nome de domínio especificado ao ativar o seu AAD para obter credenciais verificáveis.

* Hospedar o conhecido ficheiro de configuração DID na raiz do domínio.
* Não utilize redirecionamentos.
* Utilize https para distribuir o ficheiro de configuração.

>[!IMPORTANT]
>O Microsoft Authenticator não honra os redirecionamentos, o URL especificado deve ser o URL de destino final.

## <a name="user-experience"></a>Experiência do Utilizador 

Quando um utilizador está a passar por um fluxo de emissão ou a apresentar uma credencial verificável, deve saber algo sobre a organização e o seu DID. Se o domínio da nossa carteira credencial verificável, o Microsoft Authenticator, validar a relação de um DID com o domínio no documento DID e apresentar aos utilizadores duas experiências diferentes dependendo do resultado.

## <a name="verified-domain"></a>Domínio Verificado

Antes de o Microsoft Authenticator apresentar um ícone **verificado,** algumas coisas têm de ser verdadeiras:

* A assinatura do pedido de ID aberto (SIOP) auto-emitido deve ter um ponto final de serviço para o Domínio Ligado.
* O domínio raiz não utiliza um redirecionamento e utiliza https.
* O domínio listado no Documento DID tem um recurso bem conhecido.
* A credencial verificável do recurso é assinada com o mesmo DID que foi usado para assinar o SIOP que o Microsoft Authenticator usou para iniciar o fluxo.

Se todos os mencionados anteriormente forem verdadeiros, então o Microsoft Authenticator apresenta uma página verificada e inclui o domínio que foi validado.

![novo pedido de permissão](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Domínio não verificado

Se alguma das coisas acima não forem verdadeiras, o Microsoft Authenticator apresentará um aviso de página inteira ao utilizador de que o domínio não está verificado, que o utilizador está no meio de uma transação arriscada e deverá proceder com cautela. Optámos por seguir este caminho porque:

* O DID ou não está ancorado num domínio.
* A configuração não foi configurada corretamente.
* O DID com o qual o utilizador está a interagir é malicioso e na verdade não pode provar que possui um domínio (uma vez que na verdade não o faz). Devido a este último ponto, é imperativo que ligue o seu DID ao domínio que o utilizador conhece, para evitar a propagação da mensagem de aviso.

![aviso de domínio não verificado no ecrã de credencial adicionar](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Distribuir config bem conhecido

1. Navegue na página Definições em Credenciais Verificáveis e escolha **Verifique este domínio**

   ![Verifique este domínio nas definições](media/how-to-dnsbind/settings-verify.png) 

2. Descarregue o did-configuration.jsno ficheiro mostrado na imagem abaixo.

   ![Baixar config bem conhecido](media/how-to-dnsbind/verify-download.png) 

3. Copie o JWT, abra [jwt.ms](https://www.jwt.ms) e valide o domínio está correto.

4. Copie o seu DID e abra o [ION Network Explorer](https://identity.foundation/ion/explorer) para verificar se o mesmo domínio está incluído no Documento DID. 

5. Hospedar o conhecido recurso config no local especificado. Exemplo: https://www.example.com/.well-known/did-configuration.json

6. Teste a emissão ou apresentação com o Microsoft Authenticator para validar. Certifique-se de que a definição no Autenticador 'Warn about unsafe apps' está a ser alternada.

>[!NOTE]
>Por padrão, 'Warn about unsafe apps' é ligado.

Parabéns, agora você tem botas armadilhada a teia de confiança com o seu DID!

## <a name="next-steps"></a>Passos seguintes

Se durante o embarque introduzir as informações erradas de domínio de si decidir alterá-la, terá de [optar por não o fazer](how-to-opt-out.md). Neste momento, não apoiamos a atualização do seu documento DID. Optar por sair e optar por voltar a entrar criará um novo DID.