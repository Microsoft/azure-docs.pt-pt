---
title: Como revogar uma credencial verificável como emitente - Azure Ative Directory Verifiable
description: Saiba como revogar uma credencial verificável que emite
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222848"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Revogar uma credencial verificável previamente emitida (Pré-visualização)

Como parte do processo de trabalho com credenciais verificáveis (VCs), você não só tem que emitir credenciais, mas às vezes também tem que revogá-las. Neste artigo analisamos a propriedade **status** parte da especificação VC e olhamos mais de perto para o processo de revogação, por que podemos querer revogar credenciais e algumas implicações de dados e privacidade.

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Propriedade de estado na especificação de credenciais verificáveis

Antes de compreendermos as implicações da revogação de uma credencial verificável, pode ajudar a saber qual é o controlo de **estado** e como funciona hoje.

A [especificação de credenciais verificáveis W3C](https://www.w3.org/TR/vc-data-model/) refere a propriedade do **estado** na secção [4.9:](https://www.w3.org/TR/vc-data-model/#status)

"Esta especificação define a seguinte **credencial Propriedade Estatística** para a descoberta de informações sobre o estado atual de uma credencial verificável, tais como se está suspensa ou revogada."

No entanto, a especificação W3C não define um formato sobre a forma como o **controlo de estado** deve ser implementado.

"A definição do modelo de dados, formatos e protocolos para esquemas de estado estão fora de alcance para esta especificação. Existe um registo de extensão credencial verificável [VC-EXTENSION-REGISTRY] que contém sistemas de estado disponíveis para os implementadores que pretendam implementar verificação do estado de credencial verificável."

>[!NOTE]
>Por enquanto, a implementação do status check da Microsoft é proprietária, mas estamos a trabalhar ativamente com a comunidade DID para alinhar numa norma.

## <a name="how-does-the-status-property-work"></a>Como funciona a propriedade **do estado?**

Em cada credencial verificável da Microsoft, há um atributo chamado CredencialStatus. É preenchido com um estado API que a Microsoft gere em seu nome. Aqui está um exemplo do que parece.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

A fonte aberta Verifiable Credentials SDK trata de chamar o estado de API e fornecer os dados necessários.

Uma vez que a API é chamada e fornece a informação certa, a API devolverá um Verdadeiro ou Falso. Sendo verdade que a credencial verificável ainda está ativa com o Emitente e falso significando que a credencial verificável foi ativamente revogada pelo Emitente.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Por que quer revogar um VC?

Cada cliente terá a sua própria razão única para querer revogar uma credencial verificável, mas aqui estão alguns dos temas comuns que ouvimos até agora. 

- Identificação de estudante: o estudante já não é um estudante ativo na Universidade.
- Identificação do empregado: o empregado já não é um empregado ativo.
- Carta de Condução: o condutor já não vive naquele estado.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Como configurar uma credencial verificável com a capacidade de revogar

Todos os dados credenciais verificáveis não são armazenados com a Microsoft por padrão. Portanto, não temos quaisquer dados a referir para revogar um ID de credencial verificável específico. O emitente precisa de especificar um campo específico a partir do atributo credencial verificável para a Microsoft indexar e, posteriormente, sal e haxixe.

>[!NOTE]
>O hashing é uma operação criptográfica de sentido único que transforma uma entrada, chamada ```preimage``` de , e produz uma saída chamada hash que tem um comprimento fixo. Não é computacionalmente viável neste momento reverter uma operação de haxixe.

Pode dizer à Microsoft qual atributo da credencial verificável que gostaria de indexar. A implicação da indexação é que os valores indexados podem ser usados para pesquisar as suas credenciais verificáveis para os VCs que pretende revogar.

**Exemplo:** Alice é uma funcionária da Woodgrove. Alice deixou Woodgrove para trabalhar na Contoso. Jane, a administração de TI da Woodgrove, procura o e-mail da Alice na consulta de pesquisa de Credenciais Verificáveis. Neste exemplo, Jane, indexou o campo de e-mail da credencial de empregado verificada da Woodgrove. 

Veja abaixo um exemplo de como o ficheiro Regras é modificado para incluir o índice.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Apenas um atributo pode ser indexado a partir de um ficheiro Regras.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Como posso revogar uma credencial verificável

Uma vez definida uma reclamação de índice e as credenciais verificáveis foram emitidas para os seus utilizadores, é hora de ver como pode revogar uma credencial verificável na lâmina de VC.

1. Navegue para a lâmina de credenciais verificáveis no Diretório Ativo Azure.
1. Escolha a credencial verificável onde já configurar previamente a reclamação de índice e emita uma credencial verificável a um utilizador. =
1. No menu da esquerda, escolha **Revogar uma credencial** 
    ![ Revogar uma credencial](media/how-to-issuer-revoke/settings-revoke.png) 
1. Procure o atributo de índice do utilizador que pretende revogar. 

   ![Encontre a credencial para revogar](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Uma vez que estamos apenas armazenando um haxixe da reivindicação indexada da credencial verificável, apenas uma correspondência exata irá povoar os resultados da pesquisa. Pegamos na entrada como pesquisada pelo Administrador de TI e usamos o mesmo algoritmo de hashing para ver se temos uma correspondência de haxixe na nossa base de dados.
    
1. Assim que encontrar uma correspondência, **selecione** a opção Revogar à direita da credencial que pretende revogar.

   ![Um aviso que lhe diz que após a revogação o utilizador ainda tem a credencial](media/how-to-issuer-revoke/warning.png) 

1. Após a revogação bem sucedida, vê a atualização de estado e aparecerá um banner verde no topo da página. 
   ![Verifique este domínio nas definições](media/how-to-issuer-revoke/revoke-successful.png) 

Agora, sempre que uma parte dependente chama para verificar o estado desta credencial verificável específica, a API de estado da Microsoft, agindo em nome do inquilino, devolve uma resposta "falsa".

## <a name="next-steps"></a>Passos seguintes

Teste a funcionalidade por si só com uma credencial de teste para se habituar ao fluxo. Pode ver informações sobre como configurar o seu inquilino para emitir credenciais verificáveis, [revendo os nossos tutoriais.](get-started-verifiable-credentials.md)