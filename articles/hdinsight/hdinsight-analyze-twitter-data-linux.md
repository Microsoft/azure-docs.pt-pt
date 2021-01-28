---
title: Analise os dados do Twitter com a Apache Hive - Azure HDInsight
description: Aprenda a usar Apache Hive e Apache Hadoop em HDInsight para transformar dados de TWitter crus numa tabela de Colmeia pesmável.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: 74a18be37608b2ac0c8a64655fa833753111706a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942781"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analise os dados do Twitter usando Apache Hive e Apache Hadoop em HDInsight

Saiba como usar a [Colmeia Apache](https://hive.apache.org/) para processar dados do Twitter. O resultado é uma lista de utilizadores do Twitter que enviaram mais tweets que contêm uma determinada palavra.

> [!IMPORTANT]  
> Os passos deste documento foram testados no HDInsight 3.6.

## <a name="get-the-data"></a>Obter os dados

O Twitter permite-lhe recuperar os dados de cada tweet como um documento javaScript Object Notation (JSON) através de uma API REST. [OAuth](https://oauth.net) é necessário para autenticação na API.

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

1. A partir de um navegador web, inscreva-se para [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/) . Selecione a ligação **de inscrição agora** se não tiver uma conta no Twitter.

2. Selecione **Criar nova aplicação**.

3. Inserir **Nome,** **Descrição,** **Website**. Pode fazer um URL para o campo **do Site.** A tabela a seguir mostra alguns valores da amostra a utilizar:

   | Campo | Valor |
   |--- |--- |
   | Nome |MyHDInsightApp |
   | Descrição |MyHDInsightApp |
   | Site |`https://www.myhdinsightapp.com` |

4. Selecione **Sim, concordo** e, em seguida, selecione **Criar a sua aplicação twitter**.

5. Selecione o separador **Permissões.** A permissão por defeito é **apenas ler**.

6. Selecione o **separador Chaves e Tokens de acesso.**

7. **Selecione Crie o meu token de acesso**.

8. Selecione **Test OAuth** no canto superior direito da página.

9. Escreva a **chave do consumidor,** **segredo do consumidor,** **ficha de acesso** e acesso **ao segredo.**

### <a name="download-tweets"></a>Baixar tweets

O seguinte código Python descarrega 10.000 tweets do Twitter e guarda-os para um ficheiro chamado **tweets.txt**.

> [!NOTE]  
> Os seguintes passos são realizados no cluster HDInsight, uma vez que python já está instalado.

1. Utilize [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilize os seguintes comandos para instalar [tweepy,](https://www.tweepy.org/) [barra progressão,](https://pypi.python.org/pypi/progressbar/2.2)e outros pacotes necessários:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Utilize o seguinte comando para criar um ficheiro chamado **gettweets.py:**

   ```bash
   nano gettweets.py
   ```

1. Edite o código abaixo substituindo `Your consumer secret` , e com as `Your consumer key` `Your access token` `Your access token secret` informações relevantes da sua aplicação no Twitter. Em seguida, cole o código editado como o conteúdo do ficheiro **gettweets.py.**

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Ajuste o filtro de tópicos na última linha para rastrear palavras-chave populares. A utilização de palavras-chave populares no momento em que executar o script permite uma captura mais rápida de dados.

1. Utilize **ctrl + X,** em **seguida, Y** para guardar o ficheiro.

1. Utilize o seguinte comando para executar o ficheiro e descarregar tweets:

    ```bash
    python gettweets.py
    ```

    Aparece um indicador de progresso. Conta até 100% à medida que os tweets são descarregados.

   > [!NOTE]  
   > Se a barra de progresso demorar muito tempo a avançar, deve alterar o filtro para acompanhar os tópicos de tendência. Quando há muitos tweets sobre o tema no seu filtro, você pode obter rapidamente os 100 tweets necessários.

### <a name="upload-the-data"></a>Faça o upload dos dados

Para fazer o upload dos dados para o armazenamento HDInsight, utilize os seguintes comandos:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Estes comandos armazenam os dados num local a que todos os nós do cluster podem aceder.

## <a name="run-the-hiveql-job"></a>Executar o trabalho da HiveQL

1. Utilize o seguinte comando para criar um ficheiro que contenha declarações [do HiveQL:](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

   ```bash
   nano twitter.hql
   ```

    Utilize o seguinte texto como o conteúdo do ficheiro:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Prima **Ctrl + X** e, em seguida, pressione **Y** para guardar o ficheiro.

1. Utilize o seguinte comando para executar o HiveQL contido no ficheiro:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Este comando executa o ficheiro **twitter.hql.** Assim que a consulta terminar, vê-se uma `jdbc:hive2//localhost:10001/>` solicitação.

1. A partir do pedido de beeline, utilize a seguinte consulta para verificar se os dados foram importados:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Esta consulta devolve um máximo de 10 tweets que contêm a palavra **Azure** no texto da mensagem.

    > [!NOTE]  
    > Se tiver mudado o filtro no `gettweets.py` script, substitua **o Azure** por um dos filtros utilizados.

## <a name="next-steps"></a>Próximos passos

Aprendeste a transformar um conjunto de dados JSON não estruturado numa estrutura da tabela [da Colmeia Apache.](https://hive.apache.org/) Para saber mais sobre a Colmeia em HDInsight, consulte os seguintes documentos:

* [Introdução ao HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analise os dados de atraso de voo usando HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)