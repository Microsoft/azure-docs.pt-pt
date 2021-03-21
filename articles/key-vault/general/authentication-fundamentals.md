---
title: Fundamentos de autenticação do Cofre de Chaves Azure
description: Saiba como funciona o modelo de autenticação do cofre
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103572813"
---
# <a name="key-vault-authentication-fundamentals"></a>Princípios Fundamentais da Autenticação do Key Vault

O Azure Key Vault permite armazenar e gerir credenciais de aplicação de forma segura, tais como segredos, chaves e certificados num repositório de nuvem central e seguro. O Key Vault elimina a necessidade de armazenar credenciais nas suas aplicações. As suas aplicações podem autenticar-se no Key Vault na hora de executar para obter credenciais.

Como administrador, pode controlar rigorosamente quais os utilizadores e aplicações que podem aceder ao cofre-chave e pode limitar e auditar as operações que realizam. Este documento explica os conceitos fundamentais do modelo de acesso ao cofre chave. Irá fornecer-lhe um nível de conhecimento introdutório e mostrar-lhe como pode autenticar um utilizador ou aplicação para o cofre chave do início ao fim.

## <a name="required-knowledge"></a>Conhecimento Necessário

Este documento pressupõe que está familiarizado com os seguintes conceitos. Se não estiver familiarizado com nenhum destes conceitos, siga os links de ajuda antes de prosseguir.

* Ligação Azure [](../../active-directory/fundamentals/active-directory-whatis.md) Ative Directory
* Ligação dos diretores de [segurança](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Resumo dos passos de configuração do cofre da chave

1. Registe o seu utilizador ou aplicação no Azure Ative Directory como diretor de segurança.
1. Configure uma tarefa para o seu diretor de segurança no Azure Ative Directory.
1. Configure as principais políticas de acesso ao cofre para o seu diretor de segurança.
1. Configure o acesso à firewall do Cofre da Chave ao cofre da chave (opcional).
1. Teste a capacidade do seu diretor de segurança de aceder ao cofre das chaves.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registar um utilizador ou aplicação no Azure Ative Directory como diretor de segurança

Quando um utilizador ou aplicação faz um pedido para o cofre de chaves, o pedido deve primeiro ser autenticado pela Azure Ative Directory. Para que isto funcione, o utilizador ou aplicação tem de ser registado no Azure Ative Directory como diretor de segurança.

Siga os links de documentação abaixo para entender como registar um utilizador ou aplicação no Azure Ative Directory.
**Certifique-se de criar uma senha para o registo do utilizador e uma credencial de certificado secreto ou cliente do cliente para aplicações.**

* Registar um utilizador no [link](../../active-directory/fundamentals/add-users-azure-active-directory.md) Azure Ative Directory
* Registo de uma aplicação no [link](../../active-directory/develop/quickstart-register-app.md) Azure Ative Directory

## <a name="assign-your-security-principal-a-role"></a>Atribua ao seu chefe de segurança um papel

Você pode usar o controlo de acesso baseado em funções Azure (Azure RBAC) para atribuir permissões aos principais de segurança. Estas permissões são chamadas atribuições de papéis.

No contexto do cofre-chave, estas atribuições de funções determinam o nível de acesso de um diretor de segurança ao plano de gestão (também conhecido como plano de controlo) do cofre-chave. Estas atribuições de funções não fornecem acesso diretamente aos segredos do plano de dados, mas fornecem acesso para gerir propriedades do cofre de chaves. Por exemplo, um utilizador ou aplicação atribuída a uma **função Reader** não será autorizada a fazer alterações nas definições de firewall do cofre chave, enquanto um utilizador ou aplicação atribuída a uma **função de Contribuinte** pode fazer alterações. Nenhuma das funções terá acesso direto para realizar operações em segredos, chaves e certificados, tais como criar ou recuperar o seu valor até que lhes seja atribuído o acesso ao plano de dados do cofre chave. Isto está coberto no próximo passo.

>[!IMPORTANT]
> Embora os utilizadores com a função De Colaborador ou Proprietário não tenham acesso a realizar operações em segredos armazenados no cofre de chaves por padrão, as funções de Colaborador e Proprietário, fornecem permissões para adicionar ou remover políticas de acesso a segredos armazenados no cofre de chaves. Portanto, um utilizador com estas atribuições de funções pode conceder-se acesso a segredos de acesso no cofre chave. Por esta razão, recomenda-se que apenas os administradores tenham acesso às funções de Contribuinte ou Proprietário. Os utilizadores e aplicações que apenas precisam de recuperar segredos do cofre chave devem ser concedidos ao papel de Reader. **Mais detalhes na próxima secção.**

>[!NOTE]
> Quando atribuir uma atribuição de funções a um utilizador ao nível do inquilino do Azure Ative Directory, este conjunto de permissões irá escorrer para todas as subscrições, grupos de recursos e recursos no âmbito da atribuição. Para seguir o principal de menos privilégio pode fazer esta tarefa de papel num âmbito mais granular. Por exemplo, pode atribuir a um utilizador uma função Reader ao nível da subscrição e uma função Proprietário para um único cofre de tecla. Aceda às definições de Gestão de Acesso à Identidade (IAM) de uma subscrição, grupo de recursos ou cofre-chave para fazer uma atribuição de funções num âmbito mais granular.

* Para saber mais sobre a [ligação](../../role-based-access-control/built-in-roles.md) de papéis Azure
* Para saber mais sobre a atribuição ou remoção de [atribuições](../../role-based-access-control/role-assignments-portal.md) de funções link

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Configure as principais políticas de acesso ao cofre para o seu diretor de segurança

Antes de conceder acesso aos seus utilizadores e aplicações para aceder ao cofre de chaves, é importante compreender os diferentes tipos de operações que podem ser realizadas num cofre chave. Existem dois tipos principais de operações de abóbada chave, operações de avião de gestão (também designadas como avião de controlo) e operações de plano de dados.

Esta tabela mostra vários exemplos das diferentes operações que são controladas pelo plano de gestão vs o plano de dados. As operações que mudam as propriedades do cofre chave são operações de gestão de aviões. As operações que mudam ou recuperam o valor dos segredos armazenados no cofre chave são operações de data plane.

|Operações de Avião de Gestão (Exemplos)|Operações de plano de dados (exemplos)|
| --- | --- |
| Criar cofre de chaves | Criar uma chave, segredo, certificado
| Eliminar cofre de chaves | Apagar uma chave, segredo, certificado
| Adicionar ou remover atribuições de funções de função de cofre de chaves | Lista e Obter valores de Chaves, Segredos, Certificados
| Adicionar ou remover políticas de acesso ao cofre de chaves | Backup e Restaurar Chaves, Segredos, Certificados
| Modificar as definições de firewall do cofre de chaves | Renovar chaves, segredos, certificados
| Modificar as definições de recuperação do cofre de chaves | Purgar ou recuperar chaves, segredos, certificados, segredos, certificados de purga ou recuperação suave
| Modificar as definições de registos de diagnóstico do cofre de chaves

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Gestão de acesso a avião & atribuições de funções de diretório ativo Azure

As atribuições de funções do Azure Ative Directory concedem acesso à execução de operações de avião de gestão num cofre chave. Este acesso é normalmente concedido aos utilizadores, não às aplicações. Pode restringir as operações de gestão de aviões que um utilizador pode executar alterando a atribuição de funções de um utilizador.

Por exemplo, atribuir a um utilizador um Papel de Leitor de Cofre de Chave a um utilizador permitir-lhe-á ver as propriedades do cofre de chaves, como as políticas de acesso, mas não lhes permitirá fazer quaisquer alterações. Atribuindo um utilizador, uma função Proprietário permitir-lhes-á o acesso total para alterar as definições do plano de gestão do cofre chave.

As atribuições de funções são controladas na lâmina do controlo de acesso do cofre de chaves (IAM). Se quiser que um determinado utilizador tenha acesso a ser um leitor ou que seja o administrador de múltiplos recursos de cofre chave, pode criar uma atribuição de funções no nível de cofre, grupo de recursos ou subscrição, e a atribuição de funções será adicionada a todos os recursos dentro do âmbito da atribuição.

O acesso a um avião de dados ou o acesso a operações em chaves, segredos e certificados armazenados no cofre chave podem ser adicionados de uma de duas maneiras.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Opção de acesso ao plano de dados 1: Políticas clássicas de acesso ao cofre de chaves

As principais políticas de acesso ao cofre concedem aos utilizadores e aplicações acesso para realizar operações de plano de dados num cofre chave.

> [!NOTE]
> Este modelo de acesso não é compatível com o RBAC Azure para o cofre de chaves (Opção 2) documentado abaixo. Tem de escolher um. Terá a oportunidade de fazer esta seleção quando clicar no separador Política de Acesso do seu cofre chave.

As políticas clássicas de acesso são granulares, o que significa que pode permitir ou negar a capacidade de cada utilizador ou aplicação para realizar operações individuais dentro de um cofre chave. Eis alguns exemplos:

* O Diretor de Segurança 1 pode efetuar qualquer operação chave, mas não está autorizado a realizar qualquer operação secreta ou de certificado.
* O Chefe de Segurança 2 pode listar e ler todas as chaves, segredos e certificados, mas não pode realizar nenhuma criação, exclusão ou renovação de operações.
* O Diretor de Segurança 3 pode fazer backup e restaurar todos os segredos, mas não consegue ler o valor dos próprios segredos.

No entanto, as políticas clássicas de acesso não permitem permissões de nível por objeto, e as permissões atribuídas são aplicadas ao âmbito de um cofre de chaves individual. Por exemplo, se conceder a permissão de acesso "Secret Get" a um diretor de segurança num cofre particular, o diretor de segurança tem a capacidade de obter todos os segredos dentro desse cofre chave em particular. No entanto, esta permissão "Get Secret" não se estenderá automaticamente a outros cofres-chave e deve ser atribuída explicitamente.

> [!IMPORTANT]
> As políticas clássicas de acesso ao cofre e as atribuições de funções do Azure Ative Directory são independentes umas das outras. Atribuir a um diretor de segurança uma função de "Contribuinte" a nível de subscrição não permitirá automaticamente ao diretor de segurança a capacidade de realizar operações de plano de dados em cada cofre-chave dentro do âmbito da subscrição. O diretor de segurança deve ainda ser concedido, ou conceder-se permissões políticas de acesso para a realização de operações de data plane.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Opção de acesso ao plano de dados 2: Azure RBAC para cofre de chaves (pré-visualização)

Uma nova forma de conceder acesso ao plano de dados do cofre chave é através do controlo de acesso baseado em funções Azure (Azure RBAC) para o cofre chave.

> [!NOTE]
> Este modelo de acesso não é compatível com as políticas de acesso clássico do cofre acima mostradas. Tem de escolher um. Terá a oportunidade de fazer esta seleção quando clicar no separador Política de Acesso do seu cofre chave.

As atribuições de funções key Vault são um conjunto de atribuições de funções incorporadas Azure que englobam conjuntos comuns de permissões usadas para aceder a chaves, segredos e certificados. Este modelo de permissão também permite capacidades adicionais que não estão disponíveis no modelo clássico da política de acesso ao cofre.

* As permissões Azure RBAC podem ser geridas em escala, permitindo que os utilizadores tenham estas funções atribuídas a uma subscrição, grupo de recursos ou nível de cofre de chaves individuais. Um utilizador terá as permissões do plano de dados para todos os cofres chave no âmbito da atribuição do RBAC Azure. Isto elimina a necessidade de atribuir permissões de política de acesso individual por utilizador/aplicação por cofre de chaves.

* As permissões Azure RBAC são compatíveis com Gestão de Identidade Privilegiada ou PIM. Isto permite-lhe configurar controlos de acesso just-in-time para funções privilegiadas como Key Vault Administrator. Esta é uma prática de melhor segurança e segue o principal dos menos privilegiados, eliminando o acesso em pé aos seus cofres-chave.

Para saber mais sobre o Azure RBAC para Key Vault, consulte os seguintes documentos:

* Azure RBAC para [ligação](./secure-your-key-vault.md#management-plane-and-azure-rbac) key vault
* Azure RBAC para [ligação](../../role-based-access-control/built-in-roles.md#key-vault-administrator) de funções key Vault

## <a name="configure-key-vault-firewall"></a>Configure firewall de cofre de chave

Por predefinição, o cofre de chaves permite que o tráfego da internet pública envie o seu cofre chave através de um ponto final público. Para uma camada adicional de segurança, pode configurar o Azure Key Vault Firewall para restringir o acesso ao ponto final público do cofre da chave.

Para ativar a firewall do cofre de chaves, clique no separador 'Rede' no portal do cofre de teclas e selecione o botão de rádio para Permitir o acesso a partir de: "Ponto final privado e redes selecionadas". Se optar por ativar a firewall do cofre da chave, estas são as formas de permitir o tráfego através da firewall do cofre de chaves.

* Adicione endereços IPv4 à lista de autorizações de firewall do cofre de chaves. Esta opção funciona melhor para aplicações que tenham endereços IP estáticos.

* Adicione uma rede virtual à firewall do cofre da chave. Esta opção funciona melhor para os recursos Azure que têm endereços IP dinâmicos, como máquinas virtuais. Pode adicionar recursos Azure a uma rede virtual e adicionar a rede virtual à lista de autorizações de firewall do cofre chave. Esta opção utiliza um ponto final de serviço que é um endereço IP privado dentro da rede virtual. Isto fornece uma camada adicional de proteção para que nenhum tráfego entre o cofre chave e a sua rede virtual seja encaminhado através da internet pública. Para saber mais sobre o ponto final de serviço consulte a seguinte documentação. [ligação](./network-security.md)

* Adicione uma ligação de ligação privada ao cofre da chave. Esta opção liga a sua rede virtual diretamente a um determinado caso de cofre de chaves, efetivamente trazendo o cofre da chave dentro da sua rede virtual. Para saber mais sobre a configuração de uma ligação privada de ponto final ao cofre de chaves, consulte o seguinte [link](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Teste a capacidade do seu diretor de serviço de aceder ao cofre de chaves

Depois de seguir todos os passos acima, poderá definir e recuperar segredos do seu cofre chave.

### <a name="authentication-process-for-users-examples"></a>Processo de autenticação para utilizadores (exemplos)

* Os utilizadores podem iniciar sessão no portal Azure para utilizar o cofre de chaves. [Key Vault portal Quickstart](./quick-create-portal.md)

* O utilizador pode utilizar o Azure CLI para utilizar o cofre de chaves. [Key Vault Azure CLI Quickstart](./quick-create-cli.md)

* O utilizador pode utilizar o Azure PowerShell para utilizar o cofre de chaves. [Key Vault Azure PowerShell Quickstart](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Processo de autenticação do Azure Ative Directory para aplicações ou serviços (exemplos)

* Uma aplicação fornece a um cliente segredo e identificação de cliente em uma função para obter um token Azure Ative Directory. 

* Um pedido fornece um certificado para obter um token Azure Ative Directory. 

* Um recurso Azure utiliza a autenticação MSI para obter um token Azure Ative Directory. 

* Saiba mais sobre [o link](../../active-directory/managed-identities-azure-resources/overview.md) de autenticação MSI

### <a name="authentication-process-for-application-python-example"></a>Processo de autenticação para aplicação (Python Exemplo)

Utilize a seguinte amostra de código para testar se a sua aplicação pode recuperar um segredo do cofre de chaves utilizando o principal de serviço que configura.

>[!NOTE]
>Esta amostra destina-se apenas a demonstração e teste. **NÃO UTILIZE A AUTENTICAÇÃO SECRETA DO CLIENTE NA PRODUÇÃO** Esta não é uma prática de design segura. Deve utilizar o certificado de cliente ou a autenticação MSI como uma boa prática.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais detalhadamente sobre a autenticação do cofre chave, consulte o seguinte documento. [Autenticação do Key Vault](./authentication.md)
