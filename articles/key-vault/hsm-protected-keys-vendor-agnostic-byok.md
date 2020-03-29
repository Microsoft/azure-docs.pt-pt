---
title: Como gerar e transferir chaves protegidas por HSM para o Cofre de Chaves Azure - Cofre chave Azure [ Cofre de Chaves Azure ] Microsoft Docs
description: Use este artigo para ajudá-lo a planear, gerar e transferir as suas próprias chaves protegidas por HSM para usar com o Cofre chave Azure. Também conhecido como trazer a sua própria chave (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080133"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importar chaves protegidas por HSM para o Key Vault (pré-visualização)

> [!NOTE]
> Esta funcionalidade encontra-se em pré-visualização e está disponível apenas nas regiões azure *East US 2 EUAP* e *Central US EUAP*. 

Para obter uma maior garantia quando utilizar o Cofre chave Azure, pode importar ou gerar uma chave num módulo de segurança de hardware (HSM); a chave nunca deixará o limite hSM. Este cenário é muitas vezes referido como *trazer a sua própria chave* (BYOK). O Key Vault utiliza a família nCipher nShield de HSMs (FIPS 140-2 Level 2 validado) para proteger as suas chaves.

Use a informação neste artigo para ajudá-lo a planear, gerar e transferir as suas próprias chaves protegidas por HSM para usar com o Cofre chave Azure.

> [!NOTE]
> Esta funcionalidade não está disponível para o Azure China 21Vianet. 
> 
> Este método de importação está disponível apenas para [HSMs suportados](#supported-hsms). 

Para mais informações, e para que um tutorial começasse a usar o Key Vault (incluindo como criar um cofre chave para chaves protegidas por HSM), veja [o que é o Cofre chave Azure?](key-vault-overview.md)

## <a name="overview"></a>Descrição geral

Aqui está uma visão geral do processo. Os passos específicos a concluir são descritos mais tarde no artigo.

* No Cofre chave, gere uma chave (referida como *chave de troca* (KEK)). O KEK deve ser uma chave RSA-HSM que tenha apenas a `import` operação chave. Apenas o Key Vault Premium SKU suporta as teclas RSA-HSM.
* Descarregue a chave pública KEK como um ficheiro .pem.
* Transfira a chave pública KEK para um computador offline que esteja ligado a um HSM no local.
* No computador offline, utilize a ferramenta BYOK fornecida pelo seu fornecedor HSM para criar um ficheiro BYOK. 
* A chave-alvo é encriptada com um KEK, que permanece encriptado até ser transferido para o Cofre chave HSM. Apenas a versão encriptada da sua chave deixa o HSM no local.
* Um KEK que é gerado dentro de um Cofre Chave HSM não é exportável. Os HSMs impõem a regra de que não existe uma versão clara de um KEK fora de um Cofre Chave HSM.
* O KEK deve estar no mesmo cofre onde a chave-alvo será importada.
* Quando o ficheiro BYOK é enviado para o Key Vault, um Key Vault HSM utiliza a chave privada KEK para desencriptar o material-chave alvo e importá-lo como uma chave HSM. Esta operação acontece inteiramente dentro de um Cofre Chave HSM. A chave-alvo permanece sempre no limite de proteção do HSM.

## <a name="prerequisites"></a>Pré-requisitos

A tabela a seguir enumera os pré-requisitos para a utilização do BYOK no Cofre de Chaves Azure:

| Requisito | Mais informações |
| --- | --- |
| Uma subscrição do Azure. |Para criar um cofre chave no Cofre chave Azure, precisa de uma assinatura Azure. [Inscreva-se para um julgamento gratuito.](https://azure.microsoft.com/pricing/free-trial/) |
| Um SKU Premium Key Vault para importar chaves protegidas por HSM |Para obter mais informações sobre os níveis de serviço e capacidades no Cofre chave Azure, consulte [o Preço do Cofre chave](https://azure.microsoft.com/pricing/details/key-vault/). |
| Um HSM da lista de HSMs suportados e uma ferramenta BYOK e instruções fornecidas pelo seu fornecedor HSM | Deve ter permissões para um HSM e conhecimentos básicos de como usar o seu HSM. Ver [HSMs suportados](#supported-hsms). |
| Versão Azure CLI 2.1.0 ou posterior | Ver [Instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSMs suportados

|Nome do vendedor|Tipo de fornecedor|Modelos HSM suportados|Mais informações|
|---|---|---|---|
|Rio Thales|Fabricante|Família SafeNet Luna HSM 7 com versão de firmware 7.3 ou posterior| [Ferramenta e documentação SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|HSM como serviço|Serviço de Gestão de Chaves Auto-Defendedores (SDKMS)|[Exportação de chaves SDKMS para fornecedores de nuvem para BYOK - Cofre chave Azure](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Para importar chaves protegidas por HSM da família nCipher nShield de HSMs, utilize o [procedimento BYOK legado](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Tipos de chaves suportados

|Nome da chave|Tipo de chave|Tamanho da chave|Origem|Descrição|
|---|---|---|---|---|
|Chave de troca (KEK)|RSA| 2.048-bit<br />3.072 bits<br />4.096-bit|Cofre chave azure HSM|Um par de chaves RSA apoiado pelo HSM gerado no Cofre chave Azure|
|Chave-alvo|RSA|2.048-bit<br />3.072 bits<br />4.096-bit|HSM do fornecedor|A chave a ser transferida para o Cofre de Chaves Azure HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Gere e transfira a sua chave para o Cofre chave HSM

Para gerar e transferir a sua chave para um Cofre Chave HSM:

* [Passo 1: Gerar um KEK](#step-1-generate-a-kek)
* [Passo 2: Descarregue a chave pública KEK](#step-2-download-the-kek-public-key)
* [Passo 3: Gere e prepare a chave para transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passo 4: Transfira a chave para o Cofre da Chave Azure](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passo 1: Gerar um KEK

Um KEK é uma chave RSA que é gerada num Cofre HSM chave. O KEK é usado para encriptar a chave que pretende importar (a *chave-alvo).*

O KEK deve ser:
- Uma tecla RSA-HSM (2.048-bit; 3.072-bit; ou 4.096-bit)
- Gerado no mesmo cofre chave onde pretende importar a chave-alvo
- Criado com operações-chave permitidas definidopara`import`

> [!NOTE]
> O KEK deve ter a "importação" como única operação-chave permitida. A "importação" é mutuamente exclusiva com todas as outras operações-chave.

Utilize a [chave az keyvault criar](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) comando para criar `import`um KEK que tenha operações chave definidas para . Grave o identificador`kid`chave que tenha regressado do seguinte comando. (Utilizará o `kid` valor no [passo 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Passo 2: Descarregue a chave pública KEK

Utilize o download da [chave az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) para baixar a chave pública KEK para um ficheiro .pem. A chave-alvo que importa é encriptada utilizando a chave pública KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transfira o ficheiro KEKforBYOK.publickey.pem para o seu computador offline. Vai precisar deste ficheiro no próximo passo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passo 3: Gere e prepare a chave para transferência

Consulte a documentação do seu fornecedor HSM para descarregar e instalar a ferramenta BYOK. Siga as instruções do seu fornecedor HSM para gerar uma chave-alvo e, em seguida, criar um pacote de transferência chave (um ficheiro BYOK). A ferramenta BYOK `kid` utilizará o [ficheiro](#step-1-generate-a-kek) KEKforBYOK.publickey.pem que descarregou no [Passo 2](#step-2-download-the-kek-public-key) para gerar uma chave de destino encriptada num ficheiro BYOK.

Transfira o ficheiro BYOK para o computador ligado.

> [!NOTE] 
> Não é suportada a importação de chaves RSA de 1.024 bits. Atualmente, não é suportada a importação de uma chave elíptica curva (CE).
> 
> **Emissão conhecida**: Importar uma chave-alvo RSA 4K da SafeNet Luna HSMs é suportado apenas com firmware 7.4.0 ou mais recente.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passo 4: Transfira a chave para o Cofre da Chave Azure

Para completar a importação da chave, transfira o pacote de transferência chave (um ficheiro BYOK) do seu computador desligado para o computador ligado à Internet. Utilize o comando de importação da [chave az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) para carregar o ficheiro BYOK para o Cofre de Chaves HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem sucedido, o Azure CLI exibe as propriedades da chave importada.

## <a name="next-steps"></a>Passos seguintes

Agora pode usar esta chave protegida por HSM no seu cofre chave. Para mais informações, consulte [este preço e comparação de recursos.](https://azure.microsoft.com/pricing/details/key-vault/)



