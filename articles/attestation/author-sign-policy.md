---
title: Como autor e assinar uma política de Azure Attestation
description: Uma explicação de como autor e assinar uma política de atestado.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: c8ffdcd0615913649e80b20f6873d005f4ad4410
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675993"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Como autor e assinar uma política de atestado

A política de atestado é um ficheiro enviado para o Microsoft Azure Attestation. O Azure Attestation oferece a flexibilidade para carregar uma política num formato político específico de atestado. Em alternativa, uma versão codificada da política, na JSON Web Signature, também pode ser carregada. O administrador da apólice é responsável pela redação da política de atestado. Na maioria dos cenários de atestado, o partido que conta atua como administrador político. O cliente que faz a chamada de atestado envia provas de atestado, que o serviço analisa e converte em reclamações recebidas (conjunto de propriedades, valor). O serviço processa então as reclamações, com base no que está definido na apólice, e devolve o resultado calculado.

A política contém regras que determinam os critérios de autorização, propriedades e o conteúdo da ficha de atestado. Um ficheiro de política de amostras parece abaixo:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Um ficheiro de política tem três segmentos, como visto acima:

- **versão** : A versão é o número de versão da gramática que é seguida. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Atualmente, a única versão suportada é a versão 1.0.

- **regras de autorização** : Uma recolha de regras de reclamação que será verificada primeiro, para determinar se o Azure Attestation deve proceder às **regras de emissão** . As regras de reclamação aplicam-se na ordem em que são definidas.

- **regras de emissão** : Uma recolha de regras de reclamação que serão avaliadas para adicionar informações adicionais ao resultado do atestado, tal como definido na política. As regras de reclamação aplicam-se na ordem em que são definidas e são também facultativas.

Consulte [as regras de reclamação e reclamação](claim-rule-grammar.md) para obter mais informações.
   
## <a name="drafting-the-policy-file"></a>Redigir o ficheiro de política

1. Criar um novo ficheiro.
1. Adicione a versão ao ficheiro.
1. Adicione secções para **regras** de autorização e **regras de emissão** .

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  As regras de autorização contêm a ação de negação sem qualquer condição, para garantir que nenhuma regra de emissão seja processada. Em alternativa, a regra de autorização também pode conter ações de autorização,para permitir o processamento das regras de emissão.
  
4. Adicionar regras de reclamação às regras de autorização

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Se o conjunto de reclamações de entrada contiver uma reclamação correspondente ao tipo, valor e emitente, a ação de licença() dirá ao motor da política para processar as **regras de emissão** .
  
5. Adicione regras de reclamação às **regras de emissão** .

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  O conjunto de reclamações cessantes conterá uma reclamação com:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Políticas complexas podem ser concebidas de forma semelhante. Para obter mais informações, consulte [exemplos de política de atestado .](policy-examples.md)
  
6. Guarde o ficheiro.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Criação do ficheiro de política no formato JSON Web Signature

Depois de criar um ficheiro de política, para fazer o upload de uma política em formato JWS, siga os passos abaixo.

1. Gere o JWS, RFC 7515 com a política (utf-8 codificado) como a carga útil
     - O identificador de carga útil para a política codificada Base64Url deve ser "AttestationPolicy".
     
     Amostra JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (Opcional) Assine a apólice. A azure Attestation suporta os seguintes algoritmos:
     - **Nenhum:** Não assine a carga da apólice.
     - **RS256** : Algoritmo suportado para assinar a carga útil da política

3. Faça o upload do JWS e valide a apólice.
     - Se o ficheiro de política estiver isento de erros de sintaxe, o ficheiro de política é aceite pelo serviço.
     - Se o ficheiro de política contiver erros de sintaxe, o ficheiro de política é rejeitado pelo serviço.

## <a name="signing-the-policy"></a>Assinar a apólice

Abaixo está uma amostra de script Python sobre como executar a operação de assinatura de políticas

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>Passos seguintes
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
- [Atestar um enclave SGX usando amostras de código](/samples/browse/?expanded=azure&terms=attestation)