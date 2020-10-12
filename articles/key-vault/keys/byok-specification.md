---
title: Traga a sua própria especificação-chave - Azure Key Vault / Microsoft Docs
description: Este documento descrito traz a sua própria especificação chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: feef35ef86a933f32949468366fea85eb87d4866
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315784"
---
# <a name="bring-your-own-key-specification"></a>Especificação Traga a sua própria chave

Este documento descreve especificações para importar chaves protegidas pelo HSM dos HSMs no local para o Cofre-Chave.

## <a name="scenario"></a>Cenário

Um cliente do Key Vault gostaria de transferir uma chave de segurança dos seus locais HSM fora de Azure, para o cofre de azure de apoio do HSM. O processo de importação de uma chave gerada fora do Key Vault é geralmente referido como Bring Your Own Key (BYOK).

Seguem-se os requisitos:
* A chave a transferir nunca existe fora de um HSM em forma de texto simples.
* Fora de um HSM, a chave a ser transferida está sempre protegida por uma chave mantida no Cofre de Chaves Azure HSM

## <a name="terminology"></a>Terminologia

|Nome chave|Tipo chave|Origem|Descrição|
|---|---|---|---|
|Chave de troca (KEK)|RSA|Cofre de Chave Azure HSM|Um par de chaves RSA apoiado pelo HSM gerado no Cofre da Chave Azure
Chave de embrulho|AES|Fornecedor HSM|Uma chave AES [efémera] gerada pelo HSM on-prem
Chave-alvo|RSA, EC, AES|Fornecedor HSM|A chave a ser transferida para o Cofre Azure-Key HSM

**Chave de troca**: Uma chave apoiada pelo HSM que o cliente gera no cofre-chave onde a chave BYOK será importada. Este KEK deve ter as seguintes propriedades:

* É uma chave RSA-HSM (4096-bit ou 3072-bit ou 2048-bit)
* Terá key_ops fixos (APENAS 'importação'), que permitirão que seja utilizado apenas durante a BYOK
* Deve estar no mesmo cofre onde a Chave Alvo será importada.

## <a name="user-steps"></a>Passos de utilizador

Para realizar uma transferência de chave, um utilizador executa os seguintes passos:

1. Gerar KEK.
2. Recupere a chave pública do KEK.
3. Utilizando o fornecedor HSM fornecido ferramenta BYOK - Importe o KEK no HSM alvo e exporta a Chave-Alvo protegida pela KEK.
4. Importe a chave-alvo protegida para o cofre da chave Azure.

Os clientes utilizam a ferramenta BYOK e a documentação fornecida pelo fornecedor HSM para completar os Passos 3. Produz uma bolha de transferência de chave (um ficheiro ".byok").


## <a name="hsm-constraints"></a>Restrições do HSM

O HSM existente pode aplicar restrições na chave que gerem, incluindo:
* O HSM pode ter de ser configurado para permitir a exportação baseada em invólucro
* A chave-alvo pode ter de ser marcada CKA_EXTRACTABLE para o HSM permitir a exportação controlada
* Em alguns casos, a kek e a chave de embrulho podem ter de ser marcadas como CKA_TRUSTED. Isto permite que seja utilizado para embrulhar as teclas no HSM.

A configuração da origem HSM está, em geral, fora do âmbito desta especificação. A Microsoft espera que o fornecedor HSM produza documentação que acompanha a sua ferramenta BYOK para incluir quaisquer etapas de configuração.

> [!NOTE]
> Os passos 1, 2 e 4 descritos abaixo podem ser realizados usando outras interfaces como Azure PowerShell e Azure Portal. Também podem ser executados programáticamente usando funções equivalentes em Key Vault SDK.

### <a name="step-1-generate-kek"></a>Passo 1: Gerar KEK

Utilize a **chave az keyvault criar** comando para criar KEK com operações-chave definidas para importar. Note o identificador da chave 'criança' devolvido do comando abaixo.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Passo 2: Recuperar a chave pública do KEK

Descarregue a parte chave pública do KEK e guarde-a num ficheiro PEM.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Passos 3: Gerar bolha de transferência de chaves utilizando o fornecedor HSM fornecido pela ferramenta BYOK

O cliente utilizará a ferramenta HSM Vendor fornecida byOK para criar uma bolha de transferência de chave (armazenada como um ficheiro ".byok"). A tecla pública KEK (como ficheiro .pem) será uma das entradas para esta ferramenta.

#### <a name="key-transfer-blob"></a>Bolha de transferência de chaves
A longo prazo, a Microsoft gostaria de utilizar o mecanismo de CKM_RSA_AES_KEY_WRAP PKCS#11 para transferir a chave-alvo para o Azure Key Vault, uma vez que este mecanismo produz uma única bolha e, mais importante, a chave AES intermédia é manuseada pelos dois HSMs e é garantidamente efémera. Este mecanismo não está atualmente disponível em alguns HSMs, mas a combinação de proteger a chave-alvo com CKM_AES_KEY_WRAP_PAD utilizando uma chave AES e proteger a chave AES com CKM_RSA_PKCS_OAEP produz uma bolha equivalente.

O texto simples da chave-alvo depende do tipo chave: 
* Para uma chave RSA, a chave privada ASN.1 DER codificação [RFC3447] embrulhada em PKCS#8 [RFC5208] 
* Para uma chave CE, a chave privada ASN.1 DER codificação [RFC5915] embrulhada em PKCS#8 [RFC5208]
* Para uma chave de octeto, os bytes crus da chave

Os bytes para a chave de texto simples são então transformados usando o mecanismo CKM_RSA_AES_KEY_WRAP:
* Uma chave Efémera AES é gerada e encriptada com a chave RSA de embrulho utilizando RSA-OAEP com SHA1.
* A chave de texto simples codificada é encriptada utilizando a chave AES utilizando o invólucro da chave AES com o estofamento.
* A chave AES encriptada e a chave de texto liso encriptada são concatenadas para produzir a bolha de texto de cifra final.

O formato do blob de transferência utiliza a serialização compacta de encriptação web JSON (RFC7516) principalmente como veículo para entregar os metadados necessários ao serviço para uma desencriptação correta.

Se CKM_RSA_AES_KEY_WRAP_PAD for utilizada, a serialização JSON da bolha de transferência seria:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* criança = identificador chave da KEK. Para as chaves do Cofre chave, é o seguinte: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = algoritmo. 
* dir = Modo direto, ou seja, a criança referenciada é usada para proteger diretamente o texto cifrado que é uma representação precisa de CKM_RSA_AES_KEY_WRAP
* gerador = um campo informativo que denota o nome e a versão da ferramenta BYOK e do fabricante e modelo HSM de origem. Estas informações destinam-se a ser utilizadas na resolução de problemas e suporte.

A bolha JSON é armazenada num ficheiro com uma extensão ".byok" de modo a que os clientes Azure PowerShell/CLI o tratem corretamente quando são utilizados os comandos 'Add-AzKeyVaultKey' (PSH) ou 'az keyvault key import' (CLI).

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Passo 4: Carregar bolha de transferência de chave para importar chave HSM

O cliente transferirá o key transfer Blob (ficheiro ".byok") para uma estação de trabalho online e, em seguida, executará um comando **de importação chave az keyvault** para importar esta bolha como uma nova chave apoiada pelo HSM para o Key Vault. 

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```

Quando o comando acima é executado, resulta no envio de um pedido de API REST da seguinte forma:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Corpo do pedido:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```
O valor "key_hsm" é o conteúdo completo do KeyTransferPackage-ContosoFirstHSMkey.byok codificado no formato Base64.

## <a name="references"></a>Referências
- [Guia do Programador do Key Vault](../general/developers-guide.md)

## <a name="next-steps"></a>Passos seguintes
* Instruções passo a passo BYOK: [Importar chaves protegidas pelo HSM para o Cofre de Chaves (BYOK)](hsm-protected-keys-byok.md)

