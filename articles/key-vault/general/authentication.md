---
title: Autenticar para cofre de chave Azure
description: Saiba como autenticar para a Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: aaaeec3a20088cad447ab0e8034d12ef3268fae3
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793586"
---
# <a name="authenticate-to-azure-key-vault"></a>Autenticar para cofre de chave Azure

## <a name="overview"></a>Descrição geral

O Azure Key Vault é uma solução de gestão de segredos que permite centralizar o armazenamento de segredos de aplicação e controlar a sua distribuição. O Azure Key Vault elimina a necessidade de armazenar credenciais em aplicações. A sua aplicação pode autenticar-se no cofre de chaves para recuperar as credenciais necessárias. Este documento cobrirá o básico da autenticação para o cofre da chave.

Este documento irá ajudá-lo a entender como funciona a autenticação do cofre chave. Este documento irá explicar o fluxo de autenticação, mostrar-lhe como conceder acesso ao seu cofre chave, e inclui um tutorial para recuperar um segredo armazenado no cofre de chaves a partir de uma aplicação de pitão de amostra.

Este documento abrangerá:

* Conceitos-chave
* Registo principal de segurança
* Compreender o fluxo de autenticação do Cofre chave
* Conceder um acesso principal de serviço ao Key Vault
* Tutorial (Python)

## <a name="key-concepts"></a>Conceitos-chave

### <a name="azure-active-directory-concepts"></a>Conceitos de Diretório Ativo Azure

* Azure Ative Directory (AAD) - Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft, que ajuda os seus colaboradores a iniciar e aceder a recursos

* Definição de Função - Uma definição de papel é uma coleção de permissões.  A AAD tem funções padrão (Proprietário, Colaborador ou Leitor) que contêm níveis de permissões para executar operações como ler, escrever e apagar num recurso Azure. As funções também podem ser definições personalizadas criadas pelos utilizadores com permissões granulares específicas.

* Registo de Pedidos - Quando regista uma aplicação AD Azure, são criados dois objetos no seu inquilino AZure AD, um objeto de aplicação e um objeto principal de serviço. Considere o objeto de aplicação como a representação global do seu pedido de uso em todos os inquilinos, e o diretor de serviço como a representação local para uso em um inquilino específico.

### <a name="security-principal-concepts"></a>Conceitos principais de segurança

* Principal de Segurança - Um principal de segurança é um objeto que representa um utilizador, grupo, diretor de serviço ou identidade gerida que está solicitando acesso aos recursos da Azure.

* Utilizador – Um indivíduo que tem um perfil no Azure Active Directory.

* Grupo – Um conjunto de utilizadores criado no Azure Active Directory. Quando atribui uma função a um grupo, todos os utilizadores nesse grupo têm essa função.

* Principal de serviço – Uma identidade de segurança utilizada por aplicações ou serviços para aceder a recursos específicos do Azure. Pode considerá-lo como uma identidade de utilizador (nome de utilizador e palavra-passe ou certificado) para uma aplicação.

* Identidade Gerida - Uma identidade no Azure Ative Directory que é gerida automaticamente pela Azure.

* ID de objeto (ID do cliente) - um identificador único gerado pela Azure AD que está ligado a um principal de serviço durante o seu fornecimento inicial.

## <a name="security-principal-registration"></a>Registo principal de segurança

1. O administrador regista um utilizador ou aplicação (principal serviço) no Diretório Ativo Azure.

2. O administrador cria um Cofre de Chaves Azure e configura políticas de acesso (ACLs).

3. (Opcional) O administrador configura a Firewall Azure Key Vault.

![IMAGEM](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Compreender o fluxo de autenticação do Cofre-Chave

1. Um diretor de serviço faz uma chamada para autenticar a AAD isto pode acontecer de várias maneiras:
    * Um utilizador pode iniciar sessão no portal Azure utilizando um nome de utilizador e senha.
    * Uma aplicação usa um ID do cliente e apresenta um certificado de cliente secreto ou cliente à AAD
    * Um recurso Azure, como uma máquina virtual, tem um MSI atribuído e contacta o ponto final do IMDS REST para obter um token de acesso.

2. Se a autenticação para a AAD for bem sucedida, o diretor de serviço receberá um token OAuth.
3. O diretor de serviço faz uma chamada para o Key Vault.
4. A azure Key Vault Firewall determina se permite a chamada.
    * Cenário 1: Key Vault Firewall está desativado, o ponto final público (URI) do cofre chave é acessível a partir da internet pública. A chamada é permitida.
    * Cenário 2: Caller é um serviço de confiança Azure Key Vault. Certos serviços Azure podem contornar a firewall do cofre de chaves se a opção for selecionada. [Lista de serviços fidedignos de cofre de chaves](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Cenário 3: O chamador está listado na firewall Azure Key Vault por endereço IP, rede virtual ou ponto final de serviço.
    * Cenário 4: O chamador pode chegar ao Cofre da Chave Azure sobre uma ligação de ligação privada configurada.
    * Cenário 5: O chamador não está autorizado e uma resposta proibida é devolvida.
5. Key Vault faz uma chamada para a AAD para validar o token de acesso do diretor de serviço.
6. O Key Vault verifica se o diretor do serviço tem permissões de política de acesso suficientes para executar a operação solicitada, neste exemplo, a operação está em segredo.
7. Key Vault fornece o segredo para o diretor de serviço.

![IMAGEM](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Conceder um acesso principal de serviço ao Key Vault

1. Crie um diretor de serviço se ainda não tiver um. [Criar um diretor de serviço](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Adicione uma atribuição de funções ao seu principal de serviço nas definições IAM do Cofre de Chaves Azure. Pode adicionar funções pré-atribuídas de Proprietário, Colaborador ou Leitor. Também pode criar funções personalizadas para o seu principal serviço. Deve seguir o principal de menor privilégio e apenas fornecer o acesso mínimo necessário para o seu diretor de serviço. 
3.  Configure a firewall do cofre da chave. Pode manter a firewall do cofre desativada e permitir o acesso a partir da internet pública (menos segura, mais fácil de configurar). Também pode restringir o acesso a gamas IP específicas, pontos finais de serviço, redes virtuais ou pontos finais privados (mais seguros).
4.  Adicione uma política de acesso ao seu diretor de serviço, esta é uma lista de operações que o seu diretor de serviço pode executar no cofre de chaves. Deve utilizar o principal de menor privilégio e limitar as operações que o diretor de serviço pode realizar. No entanto, se não fornecer permissões suficientes, o seu chefe de serviço será impedido de ter acesso.

## <a name="tutorial"></a>Tutorial

Neste tutorial você vai aprender a configurar um diretor de serviço para autenticar para o cofre chave e recuperar um segredo. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Parte 1: Criar um diretor de serviço no portal Azure

1. Iniciar sessão no portal do Azure
1. Pesquisa rumo ao Azure Ative Directory
1. Clique no separador "Registos de Aplicações"
1. Clique em "+ Novo Registo"
1. Criar um nome para o diretor de serviço
1. Selecione Registar-se

Neste momento tem um diretor de serviço registado. Pode vê-lo selecionando "Registos de Aplicações". O seu diretor de serviço será agora designado para um cliente ID GUID, pense nisto como um "nome de utilizador" para o seu diretor de serviço. Agora precisamos de criar uma "palavra-passe" para o seu principal serviço, pode usar um segredo de cliente ou um certificado de cliente. Note que a utilização de um segredo de cliente para autenticação não é segura e deve ser utilizada apenas para fins de teste. Este tutorial irá mostrar-lhe como usar um certificado de cliente.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Parte 2: Criar um certificado de cliente para o seu principal serviço

1. Criar um certificado

    * Opção 1: Criar um certificado utilizando [o OpenSSL](https://www.openssl.org/) (apenas para efeitos de teste, não utilizar certificados auto-assinados na produção)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * Opção 2: Criar um certificado utilizando o cofre de chaves. [Criar um certificado no Cofre da Chave Azure](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Faça o download do certificado no formato PEM
1. Faça login no portal Azure e navegue para o Azure Ative Directory
1. Clique em "Registos de Aplicações"
1. Selecione o principal de serviço que criou na Parte 1.
1. Clique no separador "Certificados e Segredos" do seu principal serviço
1. Faça o upload do certificado utilizando o botão "Certificado de upload"

### <a name="part-3-configure-an-azure-key-vault"></a>Parte 3: Configurar um cofre de chaves Azure

1. Criar uma [ligação](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) de cofre de chave Azure

2. Configure permissões IAM do Cofre de Chaves
    1. Navegue até ao cofre da chave
    1. Selecione o separador "Controlo de Acesso (IAM)"
    1. Clique em Adicionar Atribuição de Função
    1. Selecione o papel "Contribuinte" a partir do dropdown
    1. Insira o nome ou identificação do cliente do principal serviço que criou
    1. Clique em "Ver Atribuições de Funções" para confirmar que o seu principal de serviço está listado

3. Configure permissões da Política de Acesso ao Cofre de Chaves
    1. Navegue até ao cofre da chave
    1. Selecione o separador "Políticas de acesso" em "Definições"
    1. Selecione o link "+ Adicionar Política de Acesso"
    1. Sob as permissões de entrega de permissões secretas verifique as permissões "Obter" e "Lista".
    1. Selecione o seu principal de serviço pelo nome ou identificação do cliente.
    1. Selecione "Adicionar"
    1. Selecione "Save"

4. Crie um segredo no seu cofre.
    1. Navegue até ao cofre da chave
    1. Clique no separador "Segredos" em Definições
    1. Clique em "+ Gerar/Importar"
    1. Criar um nome para o segredo, neste exemplo, vou nomear o "teste" secreto
    1. Crie um valor para o segredo, neste exemplo, vou definir um valor de "password123"

Agora, quando executar o código a partir da sua máquina local, pode autenticar para o cofre de chaves, obtendo um token de acesso apresentando a identificação do cliente e um caminho para o certificado.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>Parte 4: Recuperar o segredo do seu Cofre de Chaves Azure numa aplicação (Python)

Utilize a seguinte amostra de código para testar se a sua aplicação pode recuperar um segredo do cofre de chaves utilizando o principal de serviço que configura. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![IMAGEM](../media/authentication-3.png)


## <a name="next-steps"></a>Passos Seguintes

1. Saiba como resolver erros de autenticação do cofre de chaves. [Guia de resolução de problemas do cofre chave](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
