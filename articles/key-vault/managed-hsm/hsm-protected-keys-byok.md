---
title: Como gerar e transferir chaves protegidas pelo HSM para o Azure Key Vault Gerido HSM - Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planear, gerar e transferir as suas próprias chaves protegidas pelo HSM para usar com o Gerido HSM. Também conhecido como trazer a sua própria chave (BYOK).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: dd5b38a858ceba12f5d48f1782da5b85228c4b06
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212115"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Importa chaves protegidas pelo HSM para o HSM gerido (BYOK)

 Azure Key Vault Managed HSM suporta a importação de chaves geradas no seu módulo de segurança de hardware no local (HSM); as teclas nunca sairão do limite de proteção HSM. Este cenário é muitas vezes referido como *trazer a sua própria chave* (BYOK). O HSM gerido utiliza os adaptadores Marvell LiquidSecurity HSM (FIPS 140-2 Nível 3 validado) para proteger as suas chaves.

Utilize as informações deste artigo para o ajudar a planear, gerar e transferir as suas próprias chaves protegidas pelo HSM para usar com o Managed HSM.

> [!NOTE]
> Esta funcionalidade não se encontra disponível para o Azure China 21Vianet. Este método de importação só está disponível para [os HSMs suportados.](#supported-hsms) 

Para mais informações, e para que um tutorial possa começar a utilizar o HSM Gerido, veja [o que é O HSM gerido?](overview.md)

## <a name="overview"></a>Descrição Geral

Aqui está uma visão geral do processo. Os passos específicos a completar são descritos mais tarde no artigo.

* Em HSM gerido, gere uma chave (designada por *Chave de Troca* (KEK)). O KEK deve ser uma chave RSA-HSM que tem apenas a `import` operação chave. 
* Descarregue a chave pública KEK como um ficheiro .pem.
* Transfira a chave pública KEK para um computador offline que esteja ligado a um HSM no local.
* No computador offline, utilize a ferramenta BYOK fornecida pelo seu fornecedor HSM para criar um ficheiro BYOK. 
* A chave-alvo é encriptada com um KEK, que permanece encriptado até ser transferido para o HSM Gerido. Apenas a versão encriptada da sua chave deixa o HSM no local.
* Um KEK que é gerado dentro de um HSM gerido não é exportável. Os HSMs impõem a regra de que não existe uma versão clara de um KEK fora de um HSM gerido.
* O KEK deve estar no mesmo HSM gerido onde a chave-alvo será importada.
* Quando o ficheiro BYOK é carregado para O HSM Gerido, um HSM gerido utiliza a chave privada KEK para desencriptar o material-chave-alvo e importá-lo como uma chave HSM. Esta operação acontece inteiramente dentro do HSM. A chave-alvo permanece sempre no limite de proteção do HSM.


## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os comandos Azure CLI neste artigo, tem de ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A versão Azure CLI 2.12.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM gerido a [lista de HSMs suportado na](#supported-hsms) sua subscrição. Consulte [Quickstart: Provisão e ativação de um HSM gerido utilizando o Azure CLI](quick-create-cli.md) para provisões e ativar um HSM gerido.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para iniciar seduca em Azure usando o CLI pode escrever:

```azurecli
az login
```

Para obter mais informações sobre as opções de login através do CLI, dê uma olhada [no login com o Azure CLI](/cli/azure/authenticate-azure-cli)

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
|IBM|Fabricante|IBM 476x, CryptoExpress|[Fundação de Gestão chave da EMPRESA IBM](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Rio Utimaco|Fabricante,<br/>HSM como serviço|u.trust Anchor, CryptoServer|[Guia de ferramentas e integração utimaco BYOK](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Supported key types (Tipos de chaves suportados)

|Nome da chave|Tipo de chave|Tamanho/curva da chave|Origem|Descrição|
|---|---|---|---|---|
|Chave de troca (KEK)|RSA| 2.048-bit<br />3,072 bit<br />4.096-bit|HSM Gerido|Um par de chaves RSA apoiado pelo HSM gerado em HSM gerido|
|Chave-alvo|
||RSA|2.048-bit<br />3,072 bit<br />4.096-bit|Fornecedor HSM|A chave a ser transferida para o HSM gerido|
||EC|P-256<br />P-384<br />P-521|Fornecedor HSM|A chave a ser transferida para o HSM gerido|
||Chave simétrica (out-HSM)|128 bits<br />192-bit<br />256 bits|Fornecedor HSM|A chave a ser transferida para o HSM gerido|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Gerencie e transfira a sua chave para o HSM gerido

Para gerar e transferir a sua chave para um HSM gerido:

* [Passo 1: Gerar um KEK](#step-1-generate-a-kek)
* [Passo 2: Descarregue a chave pública KEK](#step-2-download-the-kek-public-key)
* [Passo 3: Gere e prepare a sua chave para a transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passo 4: Transfira a chave para O HSM Gerido](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Passo 1: Gerar um KEK

Um KEK é uma chave RSA gerada num HSM gerido. O KEK é utilizado para encriptar a chave que pretende importar (a *chave-alvo).*

O KEK deve ser:
- Uma chave RSA-HSM (2.048-bit; 3.072-bit; ou 4.096-bit)
- Gerado no mesmo HSM gerido onde pretende importar a chave-alvo
- Criado com as principais operações permitidas definidas para `import`

> [!NOTE]
> O KEK deve ter a "importação" como única operação-chave permitida. A «importação» é mutuamente exclusiva com todas as outras operações-chave.

Utilize a [chave az keyvault criar](/cli/azure/keyvault/key#az-keyvault-key-create) comando para criar um KEK que tenha as principais operações definidas para `import` . Grave o identificador da chave `kid` que é devolvido do seguinte comando. (Utilizará o `kid` valor no passo [3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Passo 2: Descarregue a chave pública KEK

Utilize [o download da chave az keyvault](/cli/azure/keyvault/key#az-keyvault-key-download) para baixar a chave pública KEK para um ficheiro .pem. A chave-alvo que importa é encriptada usando a chave pública KEK.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Transfira o ficheiro KEKforBYOK.publickey.pem para o seu computador offline. Vai precisar deste ficheiro no próximo passo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passo 3: Gere e prepare a sua chave para a transferência

Consulte a documentação do seu fornecedor HSM para descarregar e instalar a ferramenta BYOK. Siga as instruções do seu fornecedor HSM para gerar uma chave-alvo e, em seguida, crie um pacote de transferência de chaves (um ficheiro BYOK). A ferramenta BYOK utilizará o `kid` ficheiro DO [Passo 1](#step-1-generate-a-kek) e o ficheiro KEKforBYOK.publickey.pem que descarregou no [Passo 2](#step-2-download-the-kek-public-key) para gerar uma chave-alvo encriptada num ficheiro BYOK.

Transfira o ficheiro BYOK para o seu computador conectado.

> [!NOTE] 
> A importação de chaves RSA de 1.024 bits não é suportada. Atualmente, a importação de uma chave da Curva Elíptica (CE) não é suportada.
>
> **Edição conhecida**: Importar uma chave-alvo RSA 4K da Luna HSMs só é suportado com firmware 7.4.0 ou mais recente.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Passo 4: Transfira a chave para O HSM Gerido

Para completar a importação de chave, transfira o pacote de transferência de chaves (um ficheiro BYOK) do seu computador desligado para o computador ligado à Internet. Utilize o comando de importação da [chave az keyvault](/cli/azure/keyvault/key#az-keyvault-key-import) para carregar o ficheiro BYOK para o HSM Gerido.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem sucedido, o Azure CLI exibe as propriedades da chave importada.

## <a name="next-steps"></a>Passos seguintes

Agora pode utilizar esta chave protegida pelo HSM no seu HSM gerido. Para mais informações, consulte [este preço e comparação de recursos.](https://azure.microsoft.com/pricing/details/key-vault/)



