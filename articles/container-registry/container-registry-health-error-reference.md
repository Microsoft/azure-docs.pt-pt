---
title: Referência de erro para verificações de saúde
description: Códigos de erro e possíveis soluções para problemas encontrados através do comando de diagnóstico de check-health az acr no Registo de Contentores de Azure
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: a23b95ea0eaffc053c47b70107c95d2b1cdc0645
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978319"
---
# <a name="health-check-error-reference"></a>Referência de erro de verificação de saúde

Seguem-se detalhes sobre códigos de erro devolvidos pelo comando de [saúde de verificação az acr.][az-acr-check-health] Para cada erro, estão listadas possíveis soluções.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Este erro significa que o cliente do Docker para o CLI não foi encontrado. Como resultado, não são executados os seguintes controlos adicionais: encontrar a versão do Docker, avaliar o estado do Daemon do Docker e executar um comando de puxar docker.

*Soluções potenciais*: Instalar cliente Docker; adicionar o caminho do Docker para as variáveis do sistema.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Este erro significa que o estado da daemon do Docker não está disponível ou que não foi possível alcançar utilizando o CLI. Como resultado, as operações `docker login` `docker pull`do Docker (tais como e) não estão disponíveis através do CLI.

*Soluções potenciais*: Reiniciar o daemon do Docker ou validar que está corretamente instalado.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Este erro significa que o CLI `docker --version`não foi capaz de executar o comando .

*Soluções potenciais:* Tente executar o comando manualmente, certifique-se de que tem a versão CLI mais recente e investigue a mensagem de erro.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Este erro significa que o CLI não foi capaz de puxar uma imagem de amostra para o seu ambiente.

*Soluções potenciais*: Validar que todos os componentes necessários para puxar uma imagem estão a funcionar corretamente.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Este erro significa que o cliente Helm não foi encontrado pelo CLI, o que impede outras operações da Helm.

*Soluções potenciais*: Verifique se o cliente Helm está instalado e que o seu caminho é adicionado às variáveis ambientais do sistema.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Este erro significa que o CLI não foi capaz de determinar a versão Helm instalada. Isto pode acontecer se a versão Azure CLI (ou se a versão Helm) for utilizada é obsoleta.

*Soluções potenciais*: Atualizar para a versão mais recente do Azure CLI ou para a versão Helm recomendada; executar o comando manualmente e investigar a mensagem de erro.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Este erro significa que o DNS para o determinado servidor de login de registo foi pingado, mas não respondeu, o que significa que não está disponível. Isto pode indicar alguns problemas de conectividade. Em alternativa, o registo pode não existir, o utilizador pode não ter as permissões no registo (para recuperar corretamente o seu servidor de login), ou o registo-alvo encontra-se numa nuvem diferente da utilizada no ClI Azure.

*Soluções potenciais*: Validar a conectividade; Verificar a ortografia do registo e esse registo existe; verificar se o utilizador tem as permissões certas e que a nuvem do registo é a mesma que é utilizada no Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Este erro significa que o ponto final do desafio para o registo dado respondeu com um estatuto de HTTP proibido 403. Este erro significa que os utilizadores não têm acesso ao registo, muito provavelmente devido a uma configuração de rede virtual ou porque o acesso ao ponto final do registo não é permitido. Para ver as regras de firewall `az acr show --query networkRuleSet --name <registry>`atualmente configuradas, corra .

*Soluções potenciais*: Remova as regras de rede virtuais ou adicione o endereço IP do cliente atual à lista permitida.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Este erro significa que o ponto final do registo-alvo não lançou um desafio.

*Soluções potenciais*: Tente novamente após algum tempo. Se o erro persistir, abra https://aka.ms/acr/issuesum problema em .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Este erro significa que o ponto final do registo-alvo emitiu um desafio, mas o registo não suporta a autenticação do Diretório Ativo Azure.

*Soluções potenciais*: Tente uma forma diferente de autenticar, por exemplo, com credenciais de administração. Se os utilizadores precisarem de autenticar utilizando o https://aka.ms/acr/issuesDiretório Ativo Azure, abra um problema em .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Este erro significa que o servidor de login de registo não respondeu com um token de atualização, pelo que o acesso ao registo-alvo foi negado. Este erro pode ocorrer se o utilizador não tiver as permissões certas no registo ou se as credenciais de utilizador do Azure CLI estiverem velhas.

*Soluções potenciais*: Verificar se o utilizador tem as permissões certas no registo; correr `az login` para refrescar permissões, tokens e credenciais.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Este erro significa que o servidor de login de registo não respondeu com um sinal de acesso, de modo a que o acesso ao registo-alvo tenha sido negado. Este erro pode ocorrer se o utilizador não tiver as permissões certas no registo ou se as credenciais de utilizador do Azure CLI estiverem velhas.

*Soluções potenciais*: Verificar se o utilizador tem as permissões certas no registo; correr `az login` para refrescar permissões, tokens e credenciais.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Este erro significa que o cliente não conseguiu estabelecer uma ligação segura ao registo do contentor. Este erro ocorre geralmente se estiver a executar ou a utilizar um servidor proxy.

*Soluções potenciais*: Mais informações sobre o trabalho atrás de um proxy podem ser [encontradas aqui](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Este erro significa que o CLI não foi capaz de encontrar o servidor de login do registo dado, e não foi encontrado nenhum sufixo predefinido para a nuvem atual. Este erro pode ocorrer se o registo não existir, se o utilizador não tiver as permissões adequadas no registo, se a nuvem do registo e a nuvem atual do Azure CLI não corresponderem, ou se a versão Azure CLI estiver obsoleta.

*Soluções potenciais*: Verificar se a ortografia está correta e que o registo existe; Verificar se o utilizador tem as permissões adequadas no registo e que as nuvens do registo e do ambiente CLI correspondem; atualizar o Azure CLI para a versão mais recente.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Este erro significa que o CLI não é compatível com a versão atualmente instalada do Docker/Notary. Experimente reduzir a sua versão notary.exe para uma versão anterior a 0.6.0, substituindo manualmente o cliente notário da sua instalação Docker para resolver este problema.

## <a name="next-steps"></a>Passos seguintes

Para obter opções para verificar a saúde de um registo, consulte Verificar a saúde de um registo de [contentores Azure](container-registry-check-health.md).

Consulte as [FAQ](container-registry-faq.md) para perguntas frequentes e outras questões conhecidas sobre o Registo de Contentores Azure.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
