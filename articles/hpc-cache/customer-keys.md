---
title: Utilize chaves manhosas para encriptar dados em Cache HPC Azure
description: Como utilizar o Cofre de Chaves Azure com cache Azure HPC para controlar o acesso à chave de encriptação em vez de utilizar as chaves de encriptação geridas pela Microsoft predefinidas
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: v-erkel
ms.openlocfilehash: f8a8b8dfedd9c4ac0590dc91e5cdced50d2be6ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195082"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Utilize chaves de encriptação geridas pelo cliente para cache Azure HPC

Pode utilizar o Cofre de Chaves Azure para controlar a propriedade das chaves utilizadas para encriptar os seus dados em Cache HPC Azure. Este artigo explica como usar chaves geridas pelo cliente para encriptação de dados de cache.

> [!NOTE]
> Todos os dados armazenados no Azure, incluindo nos discos de cache, são encriptados em repouso utilizando por padrão as chaves geridas pela Microsoft. Só precisa de seguir os passos deste artigo se pretender gerir as chaves utilizadas para encriptar os seus dados.

Esta funcionalidade está disponível apenas nestas regiões do Azure:

* E.U.A. Leste
* E.U.A. Centro-Sul
* E.U.A.Oeste 2

Existem três passos para permitir a encriptação de chaves gerida pelo cliente para a Cache Azure HPC:

1. Instale um Cofre de Chave Azure para guardar as chaves.
1. Ao criar o Cache Azure HPC, escolha encriptação de chave gerida pelo cliente e especifique o cofre e a chave chave a utilizar.
1. Depois da cache ser criada, autorize-a a aceder ao cofre da chave.

A encriptação só está completamente configurada depois de a autorizar a partir da cache recém-criada (passo 3). Isto porque tem de passar a identidade do cache para o cofre chave para torná-lo um utilizador autorizado. Não se pode fazer isto antes de criar o cache, porque a identidade não existe até que a cache seja criada.

Depois de criar o cache, não pode alterar entre as chaves geridas pelo cliente e as chaves geridas pela Microsoft. No entanto, se o seu cache utilizar chaves geridas pelo cliente, pode [alterar](#update-key-settings) a chave de encriptação, a versão chave e o cofre de chaves, conforme necessário.

## <a name="understand-key-vault-and-key-requirements"></a>Compreender o cofre chave e os requisitos-chave

O cofre e a chave devem satisfazer estes requisitos para trabalhar com a Cache Azure HPC.

Propriedades chave do cofre:

* **Subscrição** - Utilize a mesma subscrição que é utilizada para a cache.
* **Região** - O cofre-chave deve estar na mesma região que o Azure HPC Cache.
* **Nível de preços** - O nível standard é suficiente para ser utilizado com a Cache Azure HPC.
* **Eliminação suave** - A Cache Azure HPC ativará a eliminação suave se ainda não estiver configurada no cofre da chave.
* **Proteção da purga** - A proteção da purga deve ser ativada.
* **Política de acesso** - As definições predefinidas são suficientes.
* **Conectividade de rede** - O Cache Azure HPC deve ser capaz de aceder ao cofre chave, independentemente das definições de ponto final que escolher.

Propriedades-chave:

* **Tipo de chave** - RSA
* **Tamanho da chave RSA** - 2048
* **Habilitado** - Sim

Principais permissões de acesso ao cofre:

* O utilizador que cria o Cache Azure HPC deve ter permissões equivalentes à [função](../role-based-access-control/built-in-roles.md#key-vault-contributor)de contribuinte key vault . As mesmas permissões são necessárias para configurar e gerir o Cofre chave Azure.

  Leia [o acesso seguro a um cofre](../key-vault/key-vault-secure-your-key-vault.md) para obter mais informações.

## <a name="1-set-up-azure-key-vault"></a>1. Configurar o cofre da chave Azure

Pode configurar um cofre e uma chave antes de criar a cache, ou fazê-lo como parte da criação de cache. Certifique-se de que estes recursos satisfazem os requisitos [acima](#understand-key-vault-and-key-requirements)descritos .

No momento da criação de cache deve especificar um cofre, chave e versão chave para usar para a encriptação do cache.

Leia a documentação do Cofre de [Chaves Azure](../key-vault/key-vault-overview.md) para mais detalhes.

> [!NOTE]
> O Cofre chave Azure deve utilizar a mesma subscrição e estar na mesma região que o Azure HPC Cache. Utilize uma das regiões apoiadas enumeradas no início deste artigo.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Criar a cache com chaves geridas pelo cliente ativadas

Deve especificar a fonte da chave de encriptação quando criar o seu Cache Azure HPC. Siga as instruções em [Criar uma Cache Azure HPC](hpc-cache-create.md)e especifique o cofre e a chave na página de chaves de **encriptação do Disco.** Você pode criar um novo cofre chave e chave durante a criação de cache.

> [!TIP]
> Se a página de chaves de **encriptação do Disco** não aparecer, certifique-se de que a sua cache se encontra numa das regiões suportadas.

O utilizador que cria a cache deve ter privilégios iguais ou superiores ao papel de [contribuinte key vault.](../role-based-access-control/built-in-roles.md#key-vault-contributor)

1. Clique no botão para ativar chaves geridas por privados. Depois de alterar esta definição, as definições do cofre da chave aparecem.

1. Clique **em Selecionar um cofre chave** para abrir a página de seleção da chave. Escolha ou crie o cofre e a chave para encriptar dados nos discos desta cache.

   Se o seu Cofre chave Azure não aparecer na lista, verifique estes requisitos:

   * A cache está na mesma subscrição que o cofre chave?
   * O cache na mesma região que o cofre chave?
   * Existe conectividade de rede entre o portal Azure e o cofre chave?

1. Depois de selecionar um cofre, selecione a chave individual a partir das opções disponíveis ou crie uma nova tecla. A chave deve ser uma chave RSA de 2048.

1. Especifique a versão para a chave selecionada. Saiba mais sobre a versão na documentação do [Cofre chave Azure.](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)

Continue com o resto das especificações e crie a cache conforme descrito na [Create a Azure HPC Cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autorizar encriptação do cofre de chaves Azure a partir da cache
<!-- header is linked from create article, update if changed -->

Após alguns minutos, o novo Azure HPC Cache aparece no seu portal Azure. Aceda à página **'Overview'** para autorizar o acesso ao seu Cofre de Chaves Azure e ativar a encriptação de chaves gerida pelo cliente.

> [!TIP]
> A cache pode aparecer na lista de recursos antes das mensagens de "implantação em curso". Verifique a sua lista de recursos após um minuto ou dois em vez de esperar por uma notificação de sucesso.

Este processo em duas etapas é necessário porque a instância de Cache Azure HPC precisa de uma identidade para passar para o Cofre chave Azure para autorização. A identidade da cache só existe depois de os seus passos iniciais de criação estarem completos.

> [!NOTE]
> Deve autorizar a encriptação dentro de 90 minutos após a criação da cache. Se não completar este passo, a cache vai esgotar-se e falhar. Uma cache falhada tem de ser recriada, não pode ser corrigida.

A cache mostra o estado **À espera da tecla**. Clique no botão de **encriptação Activana** parte superior da página para autorizar a cache a aceder ao cofre de chaves especificado.

![screenshot da página de visão geral da cache no portal, com destaque no botão de encriptação Enable (linha superior) e Status: À espera da chave](media/waiting-for-key.png)

Clique em **ativar a encriptação** e, em seguida, clique no botão **Sim** para autorizar a cache a utilizar a chave de encriptação. Esta ação também permite a eliminação suave e a proteção de purga (se ainda não estiver ativada) no cofre-chave.

![screenshot da página de visão geral cache no portal, com uma mensagem de banner na parte superior que pede ao utilizador para ativar a encriptação clicando sim](media/enable-keyvault.png)

Depois de a cache solicitar o acesso ao cofre da chave, pode criar e encriptar os discos que armazenam dados em cache.

Depois de autorizar a encriptação, o Azure HPC Cache passa por vários minutos de configuração para criar os discos encriptados e infraestruturas relacionadas.

## <a name="update-key-settings"></a>Atualizar definições de teclas

Pode alterar o cofre, a chave ou a versão chave para a sua cache a partir do portal Azure. Clique no link de definições de **encriptação** da cache para abrir a página de definições da **tecla do Cliente.**

Não é possível alterar uma cache entre as chaves geridas pelo cliente e as chaves geridas pelo sistema.

![screenshot da página "Definições de teclas do cliente", alcançado clicando em Definições > Encriptação a partir da página cache no portal Azure](media/change-key-click.png)

Clique no link **de tecla Alterar** e, em seguida, clique em Alterar o **cofre, a chave ou** a versão para abrir o seletor da tecla.

![screenshot da página "select key from Azure Key Vault" com três selecionadores para escolher o cofre, a chave e a versão](media/select-new-key.png)

Os cofres-chave na mesma subscrição e na mesma região que esta cache estão na lista.

Depois de escolher os novos valores da chave de encriptação, clique em **Selecionar**. Uma página de confirmação aparece com os novos valores. Clique em **Guardar** para finalizar a seleção.

![screenshot da página de confirmação com botão Guardar na parte superior esquerda](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Ler mais sobre chaves geridas pelo cliente em Azure

Estes artigos explicam mais sobre a utilização do Cofre chave Azure e chaves geridas pelo cliente para encriptar dados em Azure:

* [Visão geral da encriptação do armazenamento azure](../storage/common/storage-service-encryption.md)
* [Encriptação de disco com chaves geridas pelo cliente](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) - Documentação para a utilização do Cofre de Chaves Azure com discos geridos, que é um cenário semelhante ao Cache Azure HPC

## <a name="next-steps"></a>Passos seguintes

Depois de ter criado o Cache Azure HPC e a encriptação autorizada baseada em Key Vault, continue a configurar o seu cache dando-lhe acesso às suas fontes de dados.

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)
