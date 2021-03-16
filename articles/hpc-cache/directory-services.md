---
title: Utilize grupos estendidos em Cache Azure HPC
description: Como configurar serviços de diretório para acesso do cliente a alvos de armazenamento em Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563384"
---
# <a name="configure-directory-services"></a>Serviços de diretório de configuração

As definições **de serviços de Diretório** permitem que o seu Cache Azure HPC utilize uma fonte externa para autenticar os utilizadores para aceder ao armazenamento back-end.

Poderá ser necessário ativar **grupos estendidos** se o seu fluxo de trabalho incluir alvos de armazenamento NFS e clientes que sejam membros de mais de 16 grupos.

Depois de clicar no botão para ativar grupos estendidos, deve escolher a fonte que a Cache Azure HPC utilizará para obter credenciais de utilizador e grupo.

* [Ative Directory](#configure-active-directory) - Obtenha credenciais de um servidor de Ative Directory externo. Não podes usar o Azure Ative Directory para esta tarefa.
* [Ficheiro plano](#configure-file-download) - Descarregue `/etc/group` e `/etc/passwd` ficheiros a partir de uma localização de rede.
* [LDAP](#configure-ldap) - Obtenha credenciais de uma fonte compatível com o Protocolo de Acesso ao Diretório Leve (LDAP).

> [!NOTE]
> Certifique-se de que o seu cache pode aceder à sua fonte de informação de grupo a partir do interior da sua sub-rede segura.<!-- + details/examples -->

O campo download do **Username** mostra o estado do download de informações de grupo mais recentes.

![screenshot da página de definições de página de serviços de diretório no portal, com a opção Sim selecionada para grupos estendidos, e o menu suspenso marcado Descarregar fonte aberta](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Configurar o Active Directory

Esta secção explica como configurar a cache para obter credenciais de utilizador e grupo a partir de um servidor externo ative directory (AD).

Em **detalhes do diretório ativo,** forneça estes valores:

* **DNS Primário** - Especifique o endereço IP de um servidor de nome de domínio que a cache pode usar para resolver o nome de domínio AD.

* **DNS secundário** (opcional) - Introduza o endereço de um servidor de nome para utilizar se o servidor primário não estiver disponível.

* **Nome de domínio DNS AD** - Forneça o nome de domínio totalmente qualificado do servidor AD que a cache se juntará para obter as credenciais.

* **Nome do servidor cache (conta de computador)** - Desfie o nome que será atribuído a esta cache HPC quando se juntar ao domínio AD. Especifique um nome que seja fácil de reconhecer como esta cache. O nome pode ter até 15 caracteres de comprimento e pode incluir letras maiúsculas ou minúsculas, números e hífens (-).

Na secção **Credenciais,** forneça um nome de utilizador e senha de administrador de AD que a Cache Azure HPC pode usar para aceder ao servidor AD. Esta informação é encriptada quando armazenada e não pode ser questionada.

Guarde as definições clicando no botão na parte superior da página.

![screenshot da secção de detalhes de descarregamento com valores de Diretório Ativo preenchidos](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Configure o download de ficheiros

Estes valores são necessários se pretender descarregar ficheiros com as informações do utilizador e do grupo. Os ficheiros devem estar no formato padrão Linux/UNIX. `/etc/group` `/etc/passwrd`

* **User file URI** - Introduza o URI completo para o `/etc/passwrd` ficheiro.
* **Ficheiro de grupo URI** - Introduza o URI completo para o `/etc/group` ficheiro.

![screenshot da secção de detalhes de descarregamento para um download de ficheiros planos](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Configure LDAP

Preencha estes valores se quiser utilizar uma fonte LDAP não AD para obter credenciais de utilizador e grupo. Consulte o seu administrador LDAP se precisa de ajuda com estes valores.

* **Servidor LDAP** - Introduza o nome de domínio totalmente qualificado ou o endereço IP do servidor LDAP para utilizar. <!-- only one, not up to 3 -->

* **Base LDAP DN** - Especifique o nome distinto da base para o domínio LDAP, em formato DN. Pergunte ao seu administrador LDAP se não conhece a sua base DN.

O servidor e o DN base são as únicas definições necessárias para fazer o LDAP funcionar, mas as opções adicionais tornam a sua ligação mais segura.

![screenshot da área de configuração LDAP da página de definições de páginas de serviços de diretório](media/group-download-details-ldap.png)

Na secção **acesso Secure,** pode ativar a encriptação e validação de certificados para a ligação LDAP. Depois de clicar **Em Sim** para ativar a encriptação, tem estas opções:

* **Certificado validado** - Quando este é definido, o certificado do servidor LDAP é verificado contra a autoridade de certificados no campo URI abaixo.

* **Certificado CA URI** - Especifique o caminho para o certificado autoritário. Isto pode ser um link para um certificado validado pela AC ou para um certificado auto-assinado. Este campo é obrigado a utilizar a definição de certificados validados externamente.

* **Certificado de descarregamento automático** - Escolha **Sim** se quiser tentar descarregar um certificado assim que submeter estas definições.

Preencha a secção **de Credenciais** se quiser utilizar credenciais estáticas para a segurança LDAP. Esta informação é encriptada quando armazenada e não pode ser questionada.

* **Bind DN** - Introduza o nome distinto da ligação para utilizar para autenticar no servidor LDAP. (Use o formato DN.)
* **Vincular a palavra-passe** - Forneça a palavra-passe para o vinculante DN.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o acesso ao cliente no [Monte Azure HPC Cache](hpc-cache-mount.md)
* Se as suas credenciais não descarregarem corretamente, consulte o administrador para obter a sua origem de credenciais. Abra um [bilhete de apoio,](hpc-cache-support-ticket.md) se necessário.
