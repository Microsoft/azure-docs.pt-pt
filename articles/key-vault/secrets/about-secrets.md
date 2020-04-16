---
title: Sobre os segredos do Cofre chave Azure - Cofre chave Azure
description: Visão geral da interface rest do Cofre de Chaves Azure e detalhes do desenvolvedor para segredos.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2578f48ce218a0feaa5fb515ebc5d0e7154802ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424266"
---
# <a name="about-azure-key-vault-secrets"></a>Sobre os segredos do Cofre chave Azure

O Azure Key Vault permite que as aplicações e utilizadores do Microsoft Azure armazenem e utilizem vários tipos de dados secretos:

- Segredos: Fornece armazenamento seguro de segredos, tais como senhas e cadeias de ligação de base de dados.

- Armazenamento Azure: Pode gerir chaves de uma conta de Armazenamento Azure para si. Internamente, o Cofre chave pode listar (sincronizar) chaves com uma Conta de Armazenamento Azure e regenerar (rodar) as teclas periodicamente. 

Para obter informações mais gerais sobre o Cofre chave, veja [o que é o Cofre chave Azure?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

As seguintes secções oferecem informações gerais aplicáveis em toda a implementação do serviço Key Vault. 

### <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e versonagem

Os objetos armazenados no Cofre chave são versonizados sempre que for criada uma nova instância de um objeto. A cada versão é atribuída a um identificador e URL únicos. Quando um objeto é criado pela primeira vez, é dado um identificador de versão única e marcado como a versão atual do objeto. A criação de um novo exemplo com o mesmo nome de objeto dá ao novo objeto um identificador de versão único, fazendo com que se torne a versão atual.  

Os objetos no Cofre-Chave podem ser abordados utilizando o identificador atual ou um identificador específico da versão. Por exemplo, dada uma `MasterKey`Chave com o nome, executar operações com o identificador atual faz com que o sistema utilize a versão mais recente disponível. A realização de operações com o identificador específico da versão faz com que o sistema utilize essa versão específica do objeto.  

Os objetos são identificados exclusivamente dentro do Cofre chave utilizando um URL. Nenhum dos dois objetos no sistema tem o mesmo URL, independentemente da geolocalização. O URL completo de um objeto chama-se Identificador de Objetos. O URL consiste num prefixo que identifica o Cofre de Chave, tipo de objeto, utilizador fornecido Nome do Objeto e uma Versão de Objeto. O Nome do Objeto é insensível e imutável. Os identificadores que não incluem a Versão do Objeto são referidos como Identificadores de Base.  

Para mais informações, consulte [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)

Um identificador de objetos tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre chave no serviço Microsoft Azure Key Vault.<br /><br /> Os nomes key vault são selecionados pelo utilizador e são globalmente únicos.<br /><br /> O nome do cofre de chaves deve ser uma corda de caracteres 3-24, contendo apenas 0-9, a-z, A-Z, e ..|  
|`object-type`|O tipo de objeto, "chaves" ou "segredos".|  
|`object-name`|Um `object-name` é um utilizador fornecido nome e deve ser único dentro de um Cofre chave. O nome deve ser uma cadeia de caracteres 1-127, contendo apenas 0-9, a-z, A-Z, e - .|  
|`object-version`|Um `object-version` é um identificador de cadeia de 32 caracteres gerado pelo sistema que é opcionalmente usado para abordar uma versão única de um objeto.|  

## <a name="key-vault-secrets"></a>Segredos do Cofre chave 

### <a name="working-with-secrets"></a>Trabalhar com segredos

Do ponto de vista de um desenvolvedor, as APIs do Cofre Chave aceitam e devolvem valores secretos como cordas. Internamente, key vault armazena e gere segredos como sequências de octetos (bytes de 8 bits), com um tamanho máximo de 25 k bytes cada. O serviço Key Vault não fornece semântica para segredos. Apenas aceita os dados, encripta-os, armazena-os e devolve um identificador secreto ("id"). O identificador pode ser usado para recuperar o segredo mais tarde.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção para os dados. A encriptação de dados com uma chave de proteção separada antes do armazenamento no Key Vault é um exemplo disso.  

O Key Vault também suporta um campo de conteúdoType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar na interpretação dos dados secretos quando são recuperados. O comprimento máximo deste campo é de 255 caracteres. Não há valores pré-definidos. O uso sugerido é uma dica para interpretar os dados secretos. Por exemplo, uma implementação pode armazenar palavras-passe e certificados como segredos, em seguida, usar este campo para diferenciar. Não há valores predefinidos.  

### <a name="secret-attributes"></a>Atributos secretos

Além dos dados secretos, podem ser especificados os seguintes atributos:  

- *exp*: IntDate, opcional, o padrão é **para sempre**. O atributo *exp* (tempo de validade) identifica o tempo de validade em ou após o qual os dados secretos NÃO devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Este campo destina-se apenas a fins **informantes,** uma vez que informa os utilizadores do serviço de cofre seletiva que um determinado segredo pode não ser utilizado. O seu valor DEVE ser um número que contenha um valor IntDate.   
- *nbf*: IntDate, opcional, por defeito é **agora**. O atributo *nbf* (não antes) identifica o tempo anterior ao qual os dados secretos NÃO devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Este campo é apenas para fins **informantes.** O seu valor DEVE ser um número que contenha um valor IntDate. 
- *ativado:* booleano, opcional, padrão é **verdade**. Este atributo especifica se os dados secretos podem ser recuperados. O atributo ativado é utilizado em conjunto com a *nbf* e *exp* quando ocorre uma operação entre **true** *nbf* e *exp,* só será permitido se estiver ativado. As operações fora da janela *nbf* e *exp* são automaticamente proibidas, exceto em [situações específicas](#date-time-controlled-operations).  

Existem atributos adicionais de leitura que estão incluídos em qualquer resposta que inclua atributos secretos:  

- *criado*: IntDate, opcional. O atributo criado indica quando esta versão do segredo foi criada. Este valor é nulo para segredos criados antes da adição deste atributo. O seu valor deve ser um número que contenha um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo atualizado indica quando esta versão do segredo foi atualizada. Este valor é nulo para segredos que foram atualizados pela última vez antes da adição deste atributo. O seu valor deve ser um número que contenha um valor IntDate.

#### <a name="date-time-controlled-operations"></a>Operações controladas por data

Uma **operação** secreta funcionará por segredos ainda não válidos e expirados, fora da janela da *NBF* / *Exp.* Chamar uma **operação** secreta para um segredo ainda não válido, pode ser usado para fins de teste. Recuperar **(obter**ting) um segredo expirado, pode ser usado para operações de recuperação.

### <a name="secret-access-control"></a>Secret access control (Controlo de acesso a segredos)

O Controlo de Acesso para segredos geridos no Cofre chave é fornecido ao nível do Cofre chave que contém esses segredos. A política de controlo de acesso sacana é distinta da política de controlo de acesso para chaves no mesmo Cofre chave. Os utilizadores podem criar um ou mais cofres para guardar segredos, e são obrigados a manter o cenário apropriado de segmentação e gestão de segredos.   

As seguintes permissões podem ser usadas, por principal, nos segredos acesso à entrada de controlo de um cofre, e espelhar de perto as operações permitidas num objeto secreto:  

- Permissões para operações de gestão secreta
  - *obter*: Ler um segredo  
  - *lista*: Lista rumos os segredos ou versões de um segredo armazenado num Cofre-Chave  
  - *conjunto*: Criar um segredo  
  - *excluir*: Apagar um segredo  
  - *recuperar*: Recuperar um segredo apagado
  - *backup*: Backup um segredo em um cofre chave
  - *restaurar*: Restaurar um segredo apoiado para um cofre chave

- Permissões para operações privilegiadas
  - *Purga*: Purgar (apagar permanentemente) um segredo apagado

Para obter mais informações sobre o trabalho com segredos, consulte [operações secretas na referência da Key Vault REST API](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres - Atualizar a Política](/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso . 

### <a name="secret-tags"></a>Etiquetas secretas  
Pode especificar metadados específicos de aplicação adicionais sob a forma de etiquetas. Key Vault suporta até 15 tags, cada uma das quais pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As etiquetas são legíveis por um ouvinte se tiverem a *lista* ou *obterem* permissão.

## <a name="azure-storage-account-key-management"></a>Gestão da chave da conta de armazenamento Azure

O Cofre chave pode gerir as chaves da conta de armazenamento Azure:

- Internamente, o Key Vault pode listar chaves (sincronizadas) com uma conta de armazenamento Azure. 
- O cofre-chave regenera (gira) as teclas periodicamente.
- Os valores-chave nunca são devolvidos em resposta ao chamador.
- Key Vault gere chaves de ambas as contas de armazenamento e contas de armazenamento clássicas.

Para mais informações, consulte as chaves da conta de armazenamento do cofre de [chaves Azure)](../secrets/overview-storage-keys.md)

### <a name="storage-account-access-control"></a>Controlo de acesso à conta de armazenamento

As seguintes permissões podem ser utilizadas ao autorizar um utilizador ou um responsável de aplicação a efetuar operações numa conta de armazenamento gerida:  

- Permissões para contas de armazenamento geridas e operações de definição SaS
  - *obter*: Obtém informações sobre uma conta de armazenamento 
  - *lista*: Lista de contas de armazenamento geridas por um Cofre chave
  - *atualização*: Atualizar uma conta de armazenamento
  - *eliminar*: Eliminar uma conta de armazenamento  
  - *recuperar*: Recuperar uma conta de armazenamento eliminada
  - *backup*: Backup uma conta de armazenamento
  - *restaurar*: Restaurar uma conta de armazenamento apoiada para um cofre chave
  - *conjunto*: Criar ou atualizar uma conta de armazenamento
  - *chave regenerativa:* Regenerar um valor-chave especificado para uma conta de armazenamento
  - *getsas*: Obtenha informações sobre uma definição SAS para uma conta de armazenamento
  - *listsas*: Lista de definições SAS de armazenamento para uma conta de armazenamento
  - *deletesas*: Eliminar uma definição SAS de uma conta de armazenamento
  - *conjuntos:* Criar ou atualizar uma nova definição/atributos SAS para uma conta de armazenamento

- Permissões para operações privilegiadas
  - *purga*: Purgar (excluir permanentemente) uma conta de armazenamento gerida

Para mais informações, consulte as operações da [conta de armazenamento na referência aadia](/rest/api/keyvault)do cofre de chaves . Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres - Atualizar a Política](/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso .

## <a name="see-also"></a>Veja também

- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Cofre de Chaves](../general/developers-guide.md)
