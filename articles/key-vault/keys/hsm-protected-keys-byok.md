---
title: Como gerar & transferir chaves protegidas pelo HSM – BYOK – Azure Key Vault
description: Use este artigo para ajudá-lo a planear, gerar e transferir as suas próprias chaves protegidas pelo HSM para usar com o Cofre da Chave Azure. Também conhecido como trazer a sua própria chave (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: a1c6b054a9caac8ba223bc81e164e7ebf34bd267
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413331"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Chaves protegidas pelo HSM para o Cofre-Chave (BYOK)

Para obter uma garantia adicional quando utilizar o Azure Key Vault, pode importar ou gerar uma chave num módulo de segurança de hardware (HSM); a chave nunca deixará o limite do HSM. Este cenário é muitas vezes referido como *trazer a sua própria chave* (BYOK). O Key Vault utiliza a família nCipher nShield de HSMs (FIPS 140-2 Nível 2 validado) para proteger as suas chaves.

Use as informações neste artigo para ajudá-lo a planear, gerar e transferir as suas próprias chaves protegidas pelo HSM para usar com o Cofre da Chave Azure.

> [!NOTE]
> Esta funcionalidade não se encontra disponível para o Azure China 21Vianet. 
> 
> Este método de importação só está disponível para [os HSMs suportados.](#supported-hsms) 

Para obter mais informações, e para que um tutorial possa começar a usar o Key Vault (incluindo como criar um cofre chave para chaves protegidas pelo HSM), veja [o que é o Cofre da Chave Azure?](../general/overview.md)

## <a name="overview"></a>Descrição Geral

Aqui está uma visão geral do processo. Os passos específicos a completar são descritos mais tarde no artigo.

* No Key Vault, gere uma chave (designada por *Chave de Troca* de Chaves (KEK)). O KEK deve ser uma chave RSA-HSM que tem apenas a `import` operação chave. Apenas o Key Vault Premium SKU suporta as chaves RSA-HSM.
* Descarregue a chave pública KEK como um ficheiro .pem.
* Transfira a chave pública KEK para um computador offline que esteja ligado a um HSM no local.
* No computador offline, utilize a ferramenta BYOK fornecida pelo seu fornecedor HSM para criar um ficheiro BYOK. 
* A chave-alvo é encriptada com um KEK, que permanece encriptado até ser transferido para o Cofre de Chaves HSM. Apenas a versão encriptada da sua chave deixa o HSM no local.
* Um KEK que é gerado dentro de um Cofre de Chaves HSM não é exportável. Os HSMs impõem a regra de que não existe uma versão clara de um KEK fora de um Cofre de Chaves HSM.
* O KEK deve estar no mesmo cofre onde a chave-alvo será importada.
* Quando o ficheiro BYOK é enviado para o Key Vault, um Key Vault HSM utiliza a chave privada KEK para desencriptar o material-chave-alvo e importá-lo como uma chave HSM. Esta operação acontece inteiramente dentro de um Cofre de Chaves HSM. A chave-alvo permanece sempre no limite de proteção do HSM.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes listas de tabelas pré-requisitos para a utilização do BYOK no Cofre da Chave Azure:

| Requisito | Mais informações |
| --- | --- |
| Uma subscrição do Azure |Para criar um cofre chave no Cofre da Chave Azure, precisa de uma subscrição do Azure. [Inscreva-se para um teste gratuito.](https://azure.microsoft.com/pricing/free-trial/) |
| Um SKU-Key Vault Premium para importar chaves protegidas pelo HSM |Para obter mais informações sobre os níveis de serviço e as capacidades no Cofre da Chave Azure, consulte [o Key Vault Pricing](https://azure.microsoft.com/pricing/details/key-vault/). |
| Um HSM da lista de HSMs suportado e uma ferramenta BYOK e instruções fornecidas pelo seu fornecedor HSM | Você deve ter permissões para um HSM e conhecimento básico de como usar o seu HSM. Ver [HSMs suportados](#supported-hsms). |
| Versão Azure CLI 2.1.0 ou mais tarde | Consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSMs apoiados

|Nome do fornecedor|Tipo de Fornecedor|Modelos HSM suportados|Mais informações|
|---|---|---|---|
|nCipher|Fabricante,<br/>HSM como serviço|<ul><li>nShield família de HSMs</li><li>nShield como um serviço</ul>|[nCipher nova ferramenta e documentação BYOK](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Fabricante|<ul><li>Família Luna HSM 7 com a versão 7.3 ou mais recente</li></ul>| [Ferramenta e documentação Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Fabricante,<br/>HSM como serviço|<ul><li>Self-Defending Serviço de Gestão de Chaves (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Exportação de chaves SDKMS para fornecedores de nuvem para BYOK - Cofre chave Azure](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Rio Marvell|Fabricante|Todos os HSMs de LiquidSecurity com<ul><li>Versão firmware 2.0.4 ou posterior</li><li>Versão firmware 3.2 ou mais recente</li></ul>|[Ferramenta e documentação Marvell BYOK](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Criptomático|ISV (Sistema de Gestão de Chaves empresariais)|Múltiplas marcas e modelos HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Rio Utimaco</li></ul>Consulte [o site Cryptomathic para mais detalhes](https://www.cryptomathic.com/azurebyok)|[Ferramenta e documentação cryptomathic BYOK](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Fabricante, HSM como serviço|Família Primus HSM, Securosys Clouds HSM|[Ferramenta e documentação Primus BYOK](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Sistema de Gestão de Chaves empresariais)|Múltiplas marcas e modelos HSM, incluindo<ul><li>Rio Utimaco</li><li>Thales</li><li>nCipher</li></ul>Consulte [o site StorMagic para mais detalhes](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS e Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
||||


## <a name="supported-key-types"></a>Supported key types (Tipos de chaves suportados)

|Nome da chave|Tipo de chave|Tamanho da chave|Origem|Description|
|---|---|---|---|---|
|Chave de troca (KEK)|RSA| 2.048-bit<br />3,072 bit<br />4.096-bit|Cofre de Chave Azure HSM|Um par de chaves RSA apoiado pelo HSM gerado no Cofre da Chave Azure|
|Chave-alvo|RSA|2.048-bit<br />3,072 bit<br />4.096-bit|Fornecedor HSM|A chave a ser transferida para o Cofre Azure-Key HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Gere e transfira a sua chave para o Cofre de Chaves HSM

Para gerar e transferir a sua chave para um Cofre de Chaves HSM:

* [Passo 1: Gerar um KEK](#step-1-generate-a-kek)
* [Passo 2: Descarregue a chave pública KEK](#step-2-download-the-kek-public-key)
* [Passo 3: Gere e prepare a sua chave para a transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passo 4: Transfira a chave para o Cofre da Chave Azure](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passo 1: Gerar um KEK

Um KEK é uma chave RSA gerada num Cofre de Chaves HSM. O KEK é utilizado para encriptar a chave que pretende importar (a *chave-alvo).*

O KEK deve ser:
- Uma chave RSA-HSM (2.048-bit; 3.072-bit; ou 4.096-bit)
- Gerado no mesmo cofre-chave onde pretende importar a chave-alvo
- Criado com as principais operações permitidas definidas para `import`

> [!NOTE]
> O KEK deve ter a "importação" como única operação-chave permitida. A «importação» é mutuamente exclusiva com todas as outras operações-chave.

Utilize a [chave az keyvault criar](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) comando para criar um KEK que tenha as principais operações definidas para `import` . Grave o identificador da chave `kid` que é devolvido do seguinte comando. (Utilizará o `kid` valor no passo [3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Passo 2: Descarregue a chave pública KEK

Utilize [o download da chave az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) para baixar a chave pública KEK para um ficheiro .pem. A chave-alvo que importa é encriptada usando a chave pública KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transfira o ficheiro KEKforBYOK.publickey.pem para o seu computador offline. Vai precisar deste ficheiro no próximo passo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passo 3: Gere e prepare a sua chave para a transferência

Consulte a documentação do seu fornecedor HSM para descarregar e instalar a ferramenta BYOK. Siga as instruções do seu fornecedor HSM para gerar uma chave-alvo e, em seguida, crie um pacote de transferência de chaves (um ficheiro BYOK). A ferramenta BYOK utilizará o `kid` ficheiro DO [Passo 1](#step-1-generate-a-kek) e o ficheiro KEKforBYOK.publickey.pem que descarregou no [Passo 2](#step-2-download-the-kek-public-key) para gerar uma chave-alvo encriptada num ficheiro BYOK.

Transfira o ficheiro BYOK para o seu computador conectado.

> [!NOTE] 
> A importação de chaves RSA de 1.024 bits não é suportada. Atualmente, a importação de uma chave da Curva Elíptica (CE) não é suportada.
> 
> **Edição conhecida** : Importar uma chave-alvo RSA 4K da Luna HSMs só é suportado com firmware 7.4.0 ou mais recente.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passo 4: Transfira a chave para o Cofre da Chave Azure

Para completar a importação de chave, transfira o pacote de transferência de chaves (um ficheiro BYOK) do seu computador desligado para o computador ligado à Internet. Utilize o comando de importação da [chave az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) para carregar o ficheiro BYOK para o Cofre de Chaves HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem sucedido, o Azure CLI exibe as propriedades da chave importada.

## <a name="next-steps"></a>Passos seguintes

Agora pode utilizar esta chave protegida pelo HSM no seu cofre de chaves. Para mais informações, consulte [este preço e comparação de recursos.](https://azure.microsoft.com/pricing/details/key-vault/)



