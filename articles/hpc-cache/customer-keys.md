---
title: Use chaves de manged do cliente para encriptar dados em Cache Azure HPC
description: Como utilizar o Cofre de Chaves Azure com cache Azure HPC para controlar o acesso à chave de encriptação em vez de usar as chaves de encriptação geridas pela Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: e8f1b3fffefcdf1d2ec8bd3e9b1aaea93697ca8a
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471970"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Utilize chaves de encriptação geridas pelo cliente para cache Azure HPC

Pode utilizar o Cofre da Chave Azure para controlar a propriedade das chaves utilizadas para encriptar os seus dados na Cache Azure HPC. Este artigo explica como usar as chaves geridas pelo cliente para encriptação de dados de cache.

> [!NOTE]
> Todos os dados armazenados no Azure, incluindo nos discos de cache, são encriptados em repouso utilizando as teclas geridas pela Microsoft por padrão. Só precisa de seguir os passos deste artigo se quiser gerir as chaves utilizadas para encriptar os seus dados.

O Azure HPC Cache também está protegido pela [encriptação do anfitrião VM](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) nos discos geridos que detêm os seus dados em cache, mesmo que adicione uma chave de cliente para os discos de cache. Adicionar uma chave gerida pelo cliente para encriptação dupla dá um nível extra de segurança para clientes com elevadas necessidades de segurança. Leia [a encriptação do lado do servidor do armazenamento do disco Azure](../virtual-machines/disk-encryption.md) para obter detalhes.

<!-- This feature is available only in some of the Azure regions where Azure HPC Cache is available. Refer to the [Region availability](hpc-cache-overview.md#region-availability) list for details. -->

Existem três passos para permitir a encriptação da chave gerida pelo cliente para a Cache Azure HPC:

1. Crie um cofre de chaves Azure para guardar as chaves.
1. Ao criar o Cache Azure HPC, escolha a encriptação da chave gerida pelo cliente e especifique o cofre e a chave de utilização.
1. Depois da cache ser criada, autorize-a a aceder ao cofre da chave.

A encriptação só está completamente configurada depois de a autorizar a partir da cache recém-criada (passo 3). Isto porque tem de passar a identidade do cache para o cofre da chave para torná-lo um utilizador autorizado. Não pode fazer isso antes de criar a cache, porque a identidade não existe até que a cache seja criada.

Depois de criar o cache, não pode alterar entre as teclas geridas pelo cliente e as teclas geridas pela Microsoft. No entanto, se o seu cache utilizar chaves geridas pelo cliente, pode [alterar](#update-key-settings) a chave de encriptação, a versão chave e o cofre chave, conforme necessário.

## <a name="understand-key-vault-and-key-requirements"></a>Compreender o cofre chave e os requisitos-chave

O cofre e a chave-chave devem satisfazer estes requisitos para trabalhar com a Cache Azure HPC.

Propriedades do cofre chave:

* **Subscrição** - Utilize a mesma subscrição que é utilizada para a cache.
* **Região** - O cofre-chave deve estar na mesma região que a Cache Azure HPC.
* **Nível de preços** - O nível padrão é suficiente para ser utilizado com cache Azure HPC.
* **Eliminação suave** - Azure HPC Cache permitirá a eliminação suave se ainda não estiver configurado no cofre da chave.
* **Proteção contra purga** - A proteção da purga deve ser ativada.
* **Política de acesso** - As definições predefinidoras são suficientes.
* **Conectividade de rede** - A Azure HPC Cache deve ser capaz de aceder ao cofre da chave, independentemente das definições de ponto final que escolher.

Propriedades-chave:

* **Tipo chave** - RSA
* **Tamanho da chave RSA** - 2048
* **Habilidoso** - Sim

Permissões de acesso ao cofre chave:

* O utilizador que cria a Cache Azure HPC deve ter permissões equivalentes à [função de contribuinte Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor). As mesmas permissões são necessárias para configurar e gerir o Azure Key Vault.

  Leia [Acesso seguro a um cofre chave](../key-vault/general/secure-your-key-vault.md) para mais informações.

## <a name="1-set-up-azure-key-vault"></a>1. Configurar o Cofre da Chave Azure

Pode configurar um cofre e uma chave antes de criar a cache, ou fazê-lo como parte da criação de cache. Certifique-se de que estes recursos cumprem os requisitos [acima](#understand-key-vault-and-key-requirements)descritos.

Na hora da criação da cache, deve especificar uma versão de cofre, chave e chave para usar para a encriptação do cache.

Leia a documentação do [Cofre da Chave Azure](../key-vault/general/overview.md) para mais detalhes.

> [!NOTE]
> O Cofre da Chave Azure deve utilizar a mesma subscrição e estar na mesma região que a Cache Azure HPC. Certifique-se de que a região que escolhe [suporta a função de chaves gerida pelo cliente](hpc-cache-overview.md#region-availability).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Criar a cache com chaves geridas pelo cliente

Tem de especificar a fonte de chave de encriptação quando criar a sua Cache Azure HPC. Siga as instruções em [Criar uma Cache Azure HPC](hpc-cache-create.md)e especifique o cofre e a chave chave na página das **chaves de encriptação** do disco. Pode criar um novo cofre e chave durante a criação de cache.

> [!TIP]
> Se a página **das chaves de encriptação do disco** não aparecer, certifique-se de que o seu cache está numa das regiões suportadas.

O utilizador que criar a cache deve ter privilégios iguais ao [papel de contribuinte key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor) ou superior.

1. Clique no botão para ativar as teclas geridas por privados. Depois de alterar esta definição, aparecem as definições do cofre das chaves.

1. Clique **em Selecionar um cofre de chaves** para abrir a página de seleção de chaves. Escolha ou crie o cofre-chave e a chave para encriptar dados nos discos desta cache.

   Se o seu Cofre de Chave Azure não aparecer na lista, verifique estes requisitos:

   * A cache está na mesma subscrição que o cofre das chaves?
   * A cache é na mesma região que o cofre chave?
   * Existe conectividade de rede entre o portal Azure e o cofre de chaves?

1. Depois de selecionar um cofre, selecione a chave individual a partir das opções disponíveis ou crie uma nova chave. A chave deve ser uma chave RSA de 2048.

1. Especifique a versão para a chave selecionada. Saiba mais sobre a versão na documentação do [Cofre da Chave Azure](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Continue com o resto das especificações e crie a cache conforme descrito na [Create a Azure HPC Cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorizar a encriptação do Cofre da Chave Azure a partir da cache
<!-- header is linked from create article, update if changed -->

Após alguns minutos, o novo Azure HPC Cache aparece no seu portal Azure. Vá à página **'Vista Geral'** para autorizar o acesso ao cofre da chave Azure e ativar a encriptação de chaves gerida pelo cliente.

> [!TIP]
> A cache pode aparecer na lista de recursos antes que as mensagens "implantação em curso" se esclareçam. Verifique a sua lista de recursos após um minuto ou dois em vez de esperar por uma notificação de sucesso.

Este processo em duas etapas é necessário porque a instância cache Azure HPC precisa de uma identidade para passar para o Cofre chave Azure para autorização. A identidade da cache só existe depois de os seus primeiros passos de criação estarem completos.

> [!NOTE]
> Deve autorizar a encriptação no prazo de 90 minutos após a criação da cache. Se não completar este passo, a cache vai sair e falhar. Uma cache falhada tem de ser recriada, não pode ser corrigida.

A cache mostra o estado **à espera da chave.** Clique no botão **de encriptação Ativa** na parte superior da página para autorizar o cache a aceder ao cofre de chaves especificado.

![screenshot da página geral da cache no portal, com destaque no botão de encriptação Enable (primeira linha) e Estado: Esperando por chave](media/waiting-for-key.png)

Clique **em Ativar a encriptação** e, em seguida, clique no botão **Sim** para autorizar a cache a utilizar a chave de encriptação. Esta ação também permite eliminar e limpar a proteção (se ainda não estiver ativada) no cofre da chave.

![screenshot da página geral de cache no portal, com uma mensagem de banner no topo que pede ao utilizador para ativar a encriptação clicando sim](media/enable-keyvault.png)

Após a cache solicitar o acesso ao cofre da chave, pode criar e encriptar os discos que armazenam dados em cache.

Depois de autorizar a encriptação, a Cache Azure HPC passa por vários minutos de configuração para criar os discos encriptados e infraestruturas relacionadas.

## <a name="update-key-settings"></a>Atualizar as definições das chaves

Pode alterar o cofre, chave ou versão chave para o seu cache a partir do portal Azure. Clique no link de **definições** de encriptação do cache para abrir a página de **definições da chave cliente.**

Não é possível alterar uma cache entre as teclas geridas pelo cliente e as teclas geridas pelo sistema.

![screenshot da página "Definições de chaves do cliente", alcançada clicando em Definições > Encriptação a partir da página de cache no portal Azure](media/change-key-click.png)

Clique no link **de chave Change** e, em seguida, clique em Alterar o **cofre, chave ou versão** chave para abrir o seletor de chaves.

![screenshot da página "select key from Azure Key Vault" com três seletores para escolher o cofre, chave e versão chave](media/select-new-key.png)

Os cofres-chave na mesma subscrição e na mesma região que esta cache são mostrados na lista.

Depois de escolher os novos valores-chave de encriptação, clique em **Selecionar**. Uma página de confirmação aparece com os novos valores. Clique **em Guardar** para finalizar a seleção.

![screenshot da página de confirmação com o botão Guardar no topo esquerdo](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Leia mais sobre chaves geridas pelo cliente em Azure

Estes artigos explicam mais sobre a utilização do Cofre da Chave Azure e chaves geridas pelo cliente para encriptar dados em Azure:

* [Visão geral da encriptação de armazenamento Azure](../storage/common/storage-service-encryption.md)
* [Encriptação de disco com chaves geridas pelo cliente](../virtual-machines/disk-encryption.md#customer-managed-keys) - Documentação para a utilização do Cofre de Chaves Azure com discos geridos, que é um cenário semelhante ao cache Azure HPC

## <a name="next-steps"></a>Passos seguintes

Depois de ter criado o Cache Azure HPC e de encriptação autorizada baseada no Cofre de Chaves, continue a configurar o seu cache dando-lhe acesso às suas fontes de dados.

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)