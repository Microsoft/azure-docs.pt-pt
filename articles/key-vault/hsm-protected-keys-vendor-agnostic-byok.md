---
title: Como gerar e transferir chaves protegidas por HSM para o Cofre de Chaves Azure - Cofre chave Azure [ Cofre de Chaves Azure ] Microsoft Docs
description: Use este artigo para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para usar com o Cofre chave Azure. Também conhecido como BYOK ou traga a sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461748"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importar chaves protegidas por HSM para o Cofre-Chave (pré-visualização)

> [!NOTE]
> Esta funcionalidade encontra-se em pré-visualização e apenas disponível nas regiões **NORTE-AMERICANAS 2** E **80** 000. 

Para obter mais garantias ao utilizar o Cofre de Chaves Azure, pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca saem do limite HSM. Este cenário é muitas vezes referido como *trazer a sua própria chave*, ou BYOK. O Azure Key Vault utiliza a família nCipher nShield de HSMs (FIPS 140-2 Level 2 validado) para proteger as suas chaves.

Use a informação neste tópico para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para usar com o Cofre chave Azure.

> [!NOTE]
> Esta funcionalidade não está disponível para o Azure China 21Vianet. 
> 
> Este método de importação só está disponível para [HSMs suportados](#supported-hsms). 

Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](key-vault-overview.md)  Para começar um tutorial, que inclui a criação de um cofre chave para chaves protegidas por HSM, veja [o que é o Cofre chave Azure?](key-vault-overview.md)

## <a name="overview"></a>Descrição geral

* Gere uma chave (referida como Chave de Troca ou KEK) no cofre da chave. Esta deve ser uma chave RSA-HSM com "importação" como única operação-chave. Apenas o premium do cofre chave SKU suporta as teclas RSA-HSM.
* Descarregue a chave pública da KEK como um ficheiro .pem
* Transfira a chave pública KEK para a sua estação de trabalho offline ligada ao HSM no local.
* A partir da sua estação de trabalho offline, utilize a ferramenta BYOK fornecida pelo seu fornecedor HSM para criar um ficheiro BYOK. 
* A chave-alvo é encriptada com um KEK, que permanece encriptado até ser transferido para os HSMs do Cofre de Chaves Azure. Apenas a versão encriptada da sua chave deixa o HSM no local.
* O KEK que é gerado dentro dos HSMs do Cofre chave Azure e não é exportável. Os HSMs impõem que não pode haver uma versão clara do KEK fora dos HSMs key vault.
* O KEK deve estar no mesmo cofre onde a chave-alvo deve ser importada.
* Quando o ficheiro BYOK é enviado para o Key Vault, os HSMs key vault usam a chave privada KEK para desencriptar o material-chave alvo e importá-lo como uma chave HSM. Esta operação ocorre inteiramente dentro dos HSMs key vault e a chave-alvo permanece sempre no limite de proteção HSM.

## <a name="prerequisites"></a>Pré-requisitos

Consulte a tabela seguinte para obter uma lista de pré-requisitos para trazer a sua própria chave (BYOK) para o Cofre de Chaves Azure.

| Requisito | Mais informações |
| --- | --- |
| Uma subscrição do Azure |Para criar um Cofre chave Azure, precisa de uma subscrição Azure: [Inscreva-se para teste gratuito](https://azure.microsoft.com/pricing/free-trial/) |
| Um cofre chave (Premium SKU) para importar chaves protegidas por HSM |Para obter mais informações sobre os níveis de serviço e capacidades para o Azure Key Vault, consulte o site de preços do cofre de [chaves Azure.](https://azure.microsoft.com/pricing/details/key-vault/) |
| Um HSM da lista de HSMs suportados juntamente com a ferramenta BYOK e instruções fornecidas pelo seu fornecedor HSM | Deve ter acesso a um Módulo de Segurança de Hardware e ao conhecimento operacional básico dos seus HSMs. Ver [HSMs suportados](#supported-hsms). |
| Versão Azure CLI 2.1.0 ou mais recente | Consulte A [instalação do Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) para obter mais informações.|

## <a name="supported-hsms"></a>HSMs suportados

|Nome do fornecedor HSM|Modelos HSM suportados|Detalhes adicionais|
|---|---|---|
|Rio Thales|Família SafeNet Luna HSM 7 com versão 7.3 ou mais recente| [Ferramenta e documentação SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> Para importar chaves protegidas por HSM da família nCipher nShield de HSMs [Utilize o procedimento BYOK legado](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gere e transfira a sua chave para o Cofre de Chaves Azure HSM

Utilizará os seguintes passos para gerar e transferir a sua chave para um Cofre De Chaves Azure HSM:

* [Passo 1: Gerar um KEK](#step-1-generate-a-kek)
* [Passo 2: Baixar a chave pública KEK](#step-2-download-kek-public-key)
* [Passo 3: Gere e prepare a chave para transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Passo 4: Transfira a chave para o Cofre da Chave Azure](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Passo 1: Gerar um KEK

O KEK (Key Exchange Key Key) é uma chave RSA gerada no HSM da Key Vault. Esta chave é utilizada para encriptar a chave a importar (chave-alvo).

Kek deve ser:
1. uma chave **RSA-HSM** (2048-bit ou 3072-bit ou 4096-bit)
2. gerado no mesmo cofre chave onde pretende importar a chave alvo
3. criado com operações-chave permitidas definidos para **importar**

Utilize a [chave az keyvault criar](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) comando para criar KEK com operações chave definidas para importar. Note o identificador chave 'kid' voltou do comando abaixo. Vai precisar no [passo 3.](#step-3-generate-and-prepare-your-key-for-transfer)


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Passo 2: Baixar a chave pública KEK

Utilize o download da [chave az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) para descarregar a chave pública KEK num ficheiro .pem. A chave-alvo que importa é encriptada usando a chave pública KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transfira o ficheiro KEKforBYOK.publickey.pem para o seu posto de trabalho offline. Vai precisar deste ficheiro durante o próximo passo.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Passo 3: Gere e prepare a chave para transferência

Consulte a documentação do seu fornecedor HSM para descarregar e instalar a ferramenta BYOK. Siga as instruções do seu fornecedor HSM para gerar uma chave-alvo e, em seguida, criar um Pacote de Transferência de Chaves (um ficheiro BYOK). A ferramenta BYOK utilizará o identificador chave do [ficheiro Step 1](#step-1-generate-a-kek) e KEKforBYOK.publickey.pem que descarregou no Passo [2](#step-2-download-kek-public-key) para gerar uma chave de destino encriptada num ficheiro BYOK.

Transfira o ficheiro BYOK para a sua estação de trabalho conectada.

> [!NOTE] 
> A chave-alvo deve ser uma chave RSA de tamanho 2048-bit ou 3072-bit ou 4096-bit. Importar chaves da Curva Elíptica não é suportado neste momento.
> <br/><strong>Edição conhecida:</strong> A chave-alvo RSA 4K importada pela SafeNet Luna HSMs falha. Quando a questão for resolvida, este documento será atualizado.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Passo 4: Transfira a chave para o Cofre da Chave Azure

Para este passo final, transfira o Pacote de Transferência chave (um ficheiro BYOK) da sua estação de trabalho desligada para a estação de trabalho ligada à Internet e, em seguida, utilize o comando de importação de [chaves az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) para carregar o ficheiro BYOK do Cofre de Chaves Azure HSM, para completar a importação chave.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem sucedido, você vê exibida as propriedades da chave que acabou de importar.

## <a name="next-steps"></a>Passos seguintes

Agora pode usar esta chave protegida por HSM no seu cofre chave. Para mais informações, consulte este preço e [comparação](https://azure.microsoft.com/pricing/details/key-vault/)de recursos.
