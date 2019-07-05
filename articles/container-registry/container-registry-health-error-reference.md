---
title: Referência de erro para verificação de estado de funcionamento - Azure Container Registry
description: Códigos de erro e possíveis soluções para problemas encontrados ao executar o comando de diagnóstico de verificação do Estado de funcionamento do az acr no Azure Container Registry
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555114"
---
# <a name="health-check-error-reference"></a>Referência de erro de verificação de estado de funcionamento

Seguem-se detalhes sobre códigos de erro retornados pela [az acr verificação de estado de funcionamento da][az-acr-check-health] comando. Para cada erro, são apresentadas possíveis soluções.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Este erro significa que não foi possível encontrar cliente Docker para a CLI. Como resultado, as seguintes verificações adicionais não são executadas: encontrar estado de daemon de versão do Docker, avaliar o Docker e um Docker a executar o comando de solicitação.

*Possíveis soluções*: Instalar o cliente de Docker; Adicione o caminho de Docker para as variáveis do sistema.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Este erro significa que o estado de daemon do Docker está indisponível ou que ele não foi possível alcançar com a CLI. Como resultado, as operações de Docker (como `docker login` e `docker pull`) não estão disponíveis através da CLI.

*Possíveis soluções*: Reinicie o daemon do Docker ou validar que é instalado corretamente.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Este erro significa que não conseguiu CLI executar o comando `docker --version`.

*Possíveis soluções*: Tente executar o comando manualmente, certifique-se de que tem a versão mais recente da CLI e investigar a mensagem de erro.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Este erro significa que não conseguiu a CLI solicitar uma imagem de exemplo ao seu ambiente.

*Possíveis soluções*: Confirme que todos os componentes necessários para solicitar uma imagem estão a funcionar corretamente.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Este erro significa que o cliente Helm não foi possível encontrar a CLI, o que impede a outras operações do Helm.

*Possíveis soluções*: Certifique-se de que o cliente do Helm está instalado e que seu caminho é adicionado às variáveis de ambiente de sistema.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Este erro significa que não foi possível determinar a versão do Helm instalada a CLI. Isto pode acontecer se a versão da CLI do Azure (ou se a versão do Helm) a ser utilizada está obsoleta.

*Possíveis soluções*: Atualizar para a versão mais recente da CLI do Azure ou para a versão do Helm recomendada; Execute o comando manualmente e investigue a mensagem de erro.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Este erro significa que o DNS para o servidor de início de sessão do registo de determinado foi alcançados pelo ping, mas não respondeu, que significa que não está disponível. Isto pode indicar alguns problemas de conectividade. Em alternativa, o registro poderá não existir, o utilizador poderá não ter as permissões no Registro (para obter o respetivo servidor de início de sessão corretamente) ou o registo de destino está numa nuvem diferente daquele usado na CLI do Azure.

*Possíveis soluções*: Validar conectividade; verificar a ortografia do Registro, e esse registo existe; Certifique-se de que o utilizador tem as permissões corretas no mesmo e que a cloud do registo é o mesmo que é utilizada na CLI do Azure.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Este erro significa que o ponto de extremidade de desafio para o registo de determinado respondeu com o estado 403 Proibido HTTP. Este erro significa que os utilizadores não têm acesso ao registo, provavelmente devido a uma configuração de rede virtual.

*Possíveis soluções*: Remova as regras de rede virtual ou adicionar o endereço IP de cliente atual à lista de permitidos.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Este erro significa que o ponto de extremidade do desafio do registo de destino não emitiu um desafio.

*Possíveis soluções*: Tente novamente após algum tempo. Se o erro persistir, abra um problema em https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Este erro significa que o ponto de extremidade do desafio do Registro do destino emitido um desafio, mas o Registro não suporta a autenticação do Azure Active Directory.

*Possíveis soluções*: Tente uma maneira diferente para autenticar, por exemplo, com credenciais de administrador. Se os utilizadores têm de autenticar com o Azure Active Directory, abra um problema em https://aka.ms/acr/issues.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Este erro significa que o servidor de início de sessão do registo não respondeu com um token de atualização, pelo que foi negado o acesso ao registo de destino. Este erro pode ocorrer se o utilizador não tem as permissões corretas no Registro ou se as credenciais de utilizador para a CLI do Azure estão obsoletas.

*Possíveis soluções*: Verificar se o utilizador tem as permissões corretas no Registro; executar `az login` para atualizar as permissões, tokens e credenciais.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Este erro significa que o servidor de início de sessão do registo não respondeu com um token de acesso, para que foi negado o acesso ao registo de destino. Este erro pode ocorrer se o utilizador não tem as permissões corretas no Registro ou se as credenciais de utilizador para a CLI do Azure estão obsoletas.

*Possíveis soluções*: Verificar se o utilizador tem as permissões corretas no Registro; executar `az login` para atualizar as permissões, tokens e credenciais.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Este erro significa que a CLI não foi possível localizar o servidor de início de sessão do Registro determinado, e não foi encontrado nenhum sufixo predefinido para a cloud atual. Este erro pode ocorrer se o Registro não existir, se o utilizador não tem as permissões corretas no Registro, se na cloud o registo e a cloud atual da CLI do Azure não coincidem ou se a versão da CLI do Azure está obsoleta.

*Possíveis soluções*: Certifique-se de que a ortografia está correta e se existe o registro; Certifique-se de que o utilizador tem as permissões corretas no Registro e que correspondem a nuvens do Registro e do ambiente de CLI; Atualize a CLI do Azure para a versão mais recente.

## <a name="next-steps"></a>Passos Seguintes

Para opções para verificar o estado de funcionamento de um registo, consulte [verificar o estado de funcionamento de um Azure container registry](container-registry-check-health.md).

Consulte a [FAQ](container-registry-faq.md) para perguntas mais frequentes e outros problemas conhecidos sobre o Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
