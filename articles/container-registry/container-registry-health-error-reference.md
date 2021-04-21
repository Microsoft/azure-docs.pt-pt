---
title: Referência de erro para verificações de saúde do registo
description: Códigos de erro e possíveis soluções para problemas encontrados através da execução do comando de diagnóstico de verificação de saúde az acr no Registo do Contentor de Azure
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773446"
---
# <a name="health-check-error-reference"></a>Referência de erro de verificação de saúde

Seguem-se detalhes sobre os códigos de erro devolvidos pelo comando [de saúde de verificação az acr.][az-acr-check-health] Para cada erro, são listadas possíveis soluções.

Para obter informações sobre a `az acr check-healh` execução, consulte verificar a [saúde de um registo de contentores Azure](container-registry-check-health.md).

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Este erro significa que o cliente Docker da CLI não foi encontrado. Como resultado, não são executados os seguintes controlos adicionais: encontrar a versão Docker, avaliar o estado do Daemon do Docker e executar um comando de pull Docker.

*Soluções potenciais*: Instalar o cliente Docker; adicionar o caminho do Docker às variáveis do sistema.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Este erro significa que o estado do daemon do Docker não está disponível, ou que não foi possível chegar usando o CLI. Como resultado, as operações do Docker (tais como `docker login` `docker pull` e) não estão disponíveis através do CLI.

*Soluções potenciais*: Reinicie o daemon do Docker ou valide que está corretamente instalado.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Este erro significa que a CLI não foi capaz de executar o comando `docker --version` .

*Soluções potenciais*: Tente executar o comando manualmente, certifique-se de que tem a versão mais recente do CLI e investigue a mensagem de erro.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Este erro significa que o CLI não foi capaz de puxar uma imagem de amostra para o seu ambiente.

*Soluções potenciais*: Valide que todos os componentes necessários para puxar uma imagem estão a funcionar corretamente.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Este erro significa que o cliente Helm não foi encontrado pelo CLI, o que impede outras operações helm.

*Soluções potenciais*: Verifique se o cliente Helm está instalado e que o seu caminho é adicionado às variáveis ambientais do sistema.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Este erro significa que o CLI não foi capaz de determinar a versão Helm instalada. Isto pode acontecer se a versão Azure CLI (ou se a versão Helm) ser utilizada for obsoleta.

*Soluções potenciais*: Atualizar para a versão mais recente do Azure CLI ou para a versão Helm recomendada; executar o comando manualmente e investigar a mensagem de erro.

## <a name="cmk_error"></a>CMK_ERROR

Este erro significa que o registo não pode aceder à identidade gerida atribuída pelo utilizador ou ao sisem-designado utilizado para configurar a encriptação do registo com uma chave gerida pelo cliente. A identidade gerida pode ter sido apagada.  

*Solução potencial*: Para resolver o problema e rodar a chave utilizando uma identidade gerida diferente, consulte medidas para resolver [problemas com a identidade atribuída pelo utilizador](container-registry-customer-managed-keys.md#troubleshoot).

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Este erro significa que o DNS para o servidor de login dado foi pingado mas não respondeu, o que significa que não está disponível. Isto pode indicar alguns problemas de conectividade. Em alternativa, o registo pode não existir, o utilizador pode não ter as permissões no registo (para recuperar corretamente o seu servidor de login), ou o registo-alvo está numa nuvem diferente da utilizada no CLI Azure.

*Soluções potenciais*: Validar a conectividade; Verificar a ortografia do registo e a existência de registo; verificar se o utilizador tem as permissões certas e que a nuvem do registo é a mesma que é usada no CLI Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Este erro significa que o ponto final de desafio para o registo dado respondeu com um estado HTTP 403 Proibido. Este erro significa que os utilizadores não têm acesso ao registo, muito provavelmente devido a uma configuração de rede virtual ou porque não é permitido o acesso ao ponto final público do registo. Para ver as regras de firewall atualmente configuradas, corra `az acr show --query networkRuleSet --name <registry>` .

*Soluções potenciais*: Remova as regras de rede virtual ou adicione o endereço IP do cliente atual à lista permitida.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Este erro significa que o ponto final do registo-alvo não emitiu um desafio.

*Soluções potenciais*: Tente novamente depois de algum tempo. Se o erro persistir, abra um problema em https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Este erro significa que o ponto final de desafio do registo-alvo emitiu um desafio, mas o registo não suporta a autenticação do Azure Ative Directory.

*Soluções potenciais*: Tente uma forma diferente de autenticar, por exemplo, com credenciais de administração. Se os utilizadores precisarem de autenticar utilizando o Azure Ative Directory, abram um problema em https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Este erro significa que o servidor de login do registo não respondeu com um token de atualização, pelo que o acesso ao registo-alvo foi negado. Este erro pode ocorrer se o utilizador não tiver as permissões certas no registo ou se as credenciais de utilizador para o CLI Azure estiverem em mau estado.

*Soluções potenciais*: Verifique se o utilizador tem as permissões certas no registo; correr `az login` para refrescar permissões, fichas e credenciais.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Este erro significa que o servidor de login do registo não respondeu com um token de acesso, de modo que o acesso ao registo alvo foi negado. Este erro pode ocorrer se o utilizador não tiver as permissões certas no registo ou se as credenciais de utilizador para o CLI Azure estiverem em mau estado.

*Soluções potenciais*: Verifique se o utilizador tem as permissões certas no registo; correr `az login` para refrescar permissões, fichas e credenciais.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Este erro significa que o cliente não foi capaz de estabelecer uma ligação segura ao registo do contentor. Este erro ocorre geralmente se estiver a executar ou a utilizar um servidor proxy.

*Soluções potenciais*: Mais informações sobre o trabalho por trás de um representante podem ser [encontradas aqui](/cli/azure/use-cli-effectively).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Este erro significa que o CLI não foi capaz de encontrar o servidor de login do registo dado, e não foi encontrado nenhum sufixo padrão para a nuvem atual. Este erro pode ocorrer se o registo não existir, se o utilizador não tiver as permissões certas no registo, se a nuvem do registo e a nuvem atual do Azure CLI não corresponderem, ou se a versão Azure CLI for obsoleta.

*Soluções potenciais*: Verifique se a ortografia está correta e que o registo existe; Verificar se o utilizador tem as permissões certas no registo e que as nuvens do registo e do ambiente CLI coincidem; atualizar O Azure CLI para a versão mais recente.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Este erro significa que o CLI não é compatível com a versão atualmente instalada do Docker/Notário. Tente reduzir a sua versão notary.exe para uma versão anterior a 0.6.0 substituindo manualmente o cliente notário da sua instalação Docker para resolver este problema.

## <a name="next-steps"></a>Passos seguintes

Para obter opções para verificar a saúde de um registo, consulte [a saúde de um registo de contentores Azure](container-registry-check-health.md).

Consulte as [FAQ](container-registry-faq.md) para obter perguntas frequentes e outras questões conhecidas sobre o Registo de Contentores Azure.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
