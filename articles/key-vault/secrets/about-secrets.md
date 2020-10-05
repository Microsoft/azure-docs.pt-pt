---
title: Sobre os segredos do Cofre da Chave Azure - Azure Key Vault
description: Visão geral da interface Azure Key Vault REST e detalhes do desenvolvedor para segredos.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 7aa2feba5a2b2fa47bbb0c055a2f556b8997ab34
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "82930476"
---
# <a name="about-azure-key-vault-secrets"></a>Sobre os segredos do Cofre da Chave Azure

O Key Vault fornece um armazenamento seguro de segredos, tais como palavras-passe e cadeias de ligação de bases de dados.

Do ponto de vista de um desenvolvedor, as APIs do Cofre chave aceitam e devolvem valores secretos como cordas. Internamente, o Key Vault armazena e gere segredos como sequências de octetos (bytes de 8 bits), com um tamanho máximo de 25k bytes cada. O serviço Key Vault não fornece semântica para segredos. Apenas aceita os dados, encripta-os, armazena-os e devolve um identificador secreto ("id"). O identificador pode ser usado para recuperar o segredo mais tarde.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção para os dados. A encriptação de dados com uma chave de proteção separada antes do armazenamento no Key Vault é um exemplo disso.  

O Key Vault também suporta um campo de conteúdosType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar na interpretação dos dados secretos quando estes são recuperados. O comprimento máximo deste campo é de 255 caracteres. Não há valores pré-definidos. O uso sugerido é como uma dica para interpretar os dados secretos. Por exemplo, uma implementação pode armazenar senhas e certificados como segredos, em seguida, usar este campo para diferenciar. Não há valores predefinidos.  

## <a name="encryption"></a>Encriptação

Todos os segredos no seu Cofre de Chaves estão armazenados encriptados. Esta encriptação é transparente e não requer qualquer ação do utilizador. O serviço Azure Key Vault encripta os seus segredos quando os adiciona e desencripta-os automaticamente quando os lê. A chave de encriptação é única em cada cofre de chaves.

## <a name="secret-attributes"></a>Atributos secretos

Além dos dados secretos, podem ser especificados os seguintes atributos:  

- *exp*: IntDate, opcional, o padrão é **para sempre**. O atributo *exp* (prazo de validade) identifica o tempo de validade em ou após o qual os dados secretos não devem ser recuperados, exceto em [situações particulares](#date-time-controlled-operations). Este campo destina-se apenas a fins **informativos,** uma vez que informa os utilizadores do serviço de cofre-chave que um determinado segredo não pode ser usado. O seu valor DEVE ser um número que contém um valor IntDate.   
- *nbf*: IntDate, opcional, o padrão é **agora**. O *atributo nbf* (não antes) identifica o tempo anterior ao qual os dados secretos não devem ser recuperados, exceto em [situações particulares](#date-time-controlled-operations). Este campo é apenas para fins **informativos.** O seu valor DEVE ser um número que contém um valor IntDate. 
- *ativado:* boolean, opcional, o padrão é **verdadeiro**. Este atributo especifica se os dados secretos podem ser recuperados. O atributo ativado é utilizado em conjunto com *a NBF* e *exp* quando ocorre uma operação entre *a NBF* e *a exp,* só será permitido se o habilitado for definido como **verdadeiro**. As operações fora da janela *nbf* e *exp* são automaticamente proibidas, exceto em [situações particulares](#date-time-controlled-operations).  

Existem atributos adicionais apenas de leitura que estão incluídos em qualquer resposta que inclua atributos secretos:  

- *criado*: IntDate, opcional. O atributo criado indica quando esta versão do segredo foi criada. Este valor é nulo para segredos criados antes da adição deste atributo. O seu valor deve ser um número que contenha um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo atualizado indica quando esta versão do segredo foi atualizada. Este valor é nulo para segredos que foram atualizados pela última vez antes da adição deste atributo. O seu valor deve ser um número que contenha um valor IntDate.

### <a name="date-time-controlled-operations"></a>Operações controladas à data

Uma **operação** secreta funcionará para segredos ainda não válidos e expirados, fora da janela *da NBF*  /  *Exp.* Chamar uma **operação** secreta, para um segredo ainda não válido, pode ser usado para fins de teste. A recuperação **(get**ting) de um segredo expirado, pode ser usada para operações de recuperação.

## <a name="secret-access-control"></a>Secret access control (Controlo de acesso a segredos)

O Controlo de Acesso para segredos geridos em Key Vault, é fornecido ao nível do Cofre de Chaves que contém esses segredos. A política de controlo de acesso para segredos.é distinta da política de controlo de acesso para chaves no mesmo Cofre chave. Os utilizadores podem criar um ou mais cofres para guardar segredos, e são obrigados a manter o cenário de segmentação e gestão de segredos apropriados.   

As seguintes permissões podem ser utilizadas, numa base por princípio, na entrada de controlo de acesso aos segredos num cofre e espelhar de perto as operações permitidas num objeto secreto:  

- Permissões para operações de gestão secreta
  - *get*: Ler um segredo  
  - *lista*: Listar os segredos ou versões de um segredo armazenado num Cofre de Chaves  
  - *conjunto*: Criar um segredo  
  - *excluir*: Eliminar um segredo  
  - *recuperar*: Recuperar um segredo apagado
  - *backup*: Apoiar um segredo em um cofre chave
  - *restaurar:* Restaurar um segredo apoiado para um cofre chave

- Permissões para operações privilegiadas
  - *purga:* Purgar (apagar permanentemente) um segredo apagado

Para obter mais informações sobre o trabalho com segredos, consulte [as operações secretas na referência da API key Vault REST](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Abóbadas - Atualizar a Política de Acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="secret-tags"></a>Etiquetas secretas  
Pode especificar metadados específicos de aplicação adicionais sob a forma de etiquetas. O Key Vault suporta até 15 tags, cada uma das quais pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão.

## <a name="azure-storage-account-key-management"></a>Gestão de chaves de conta de armazenamento Azure

Key Vault pode gerir as chaves da conta de armazenamento Azure:

- Internamente, o Key Vault pode listar as chaves (sincronização) com uma conta de armazenamento Azure. 
- O Cofre-chave regenera (gira) as chaves periodicamente.
- Os valores-chave nunca são devolvidos em resposta ao chamador.
- Key Vault gere chaves de contas de armazenamento e contas de armazenamento clássicas.

Para mais informações, consulte [as chaves da conta de armazenamento do cofre da chave Azure](../secrets/overview-storage-keys.md))

## <a name="storage-account-access-control"></a>Controlo de acesso à conta de armazenamento

As seguintes permissões podem ser utilizadas ao autorizar um utilizador ou o principal de aplicação a realizar operações numa conta de armazenamento gerida:  

- Permissões para conta de armazenamento gerida e operações de definição de SaS
  - *obter*: Obtém informações sobre uma conta de armazenamento 
  - *lista*: Lista de contas de armazenamento geridas por um Cofre-Chave
  - *atualização*: Atualizar uma conta de armazenamento
  - *excluir*: Eliminar uma conta de armazenamento  
  - *recuperar*: Recuperar uma conta de armazenamento eliminada
  - *backup*: Faça backup de uma conta de armazenamento
  - *restaurar*: Restaurar uma conta de armazenamento apoiada para um Cofre-Chave
  - *conjunto*: Criar ou atualizar uma conta de armazenamento
  - *regeneração :* Regenerar um valor-chave especificado para uma conta de armazenamento
  - *getsas*: Obtenha informações sobre uma definição SAS para uma conta de armazenamento
  - *listsas*: Listar as definições SAS de armazenamento para uma conta de armazenamento
  - *deletesas*: Eliminar uma definição SAS de uma conta de armazenamento
  - *setsas*: Criar ou atualizar uma nova definição/atributos SAS para uma conta de armazenamento

- Permissões para operações privilegiadas
  - *purga:* Purga (eliminar permanentemente) uma conta de armazenamento gerida

Para obter mais informações, consulte as operações da [conta de armazenamento na referência API do Cofre-Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Abóbadas - Atualizar a Política de Acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Passos seguintes

- [Sobre o Key Vault](../general/overview.md)
- [Acerca de chaves, segredos e certificados](../general/about-keys-secrets-certificates.md)
- [Acerca de chaves](../keys/about-keys.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
