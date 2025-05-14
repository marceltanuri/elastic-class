---
marp: true
theme: default
paginate: true
header: "Elasticsearch e Kibana para Desenvolvedores Backend Meetup - ADA | MELI 1321 + 1322"
footer: "By Marcel Tanuri"
style: |
  section {
    background-color: #1e1e1e;
    color: white;
  }
---

# Elasticsearch e Kibana
## Para Desenvolvedores Backend com Spring Boot

---

# Objetivo
- Entender o que são Elasticsearch e Kibana
- Aprender como configurá-los para monitorar aplicações Spring Boot
- Explorar outras possibilidades de uso

---

# O que é Observabilidade?
- Capacidade de monitorar e entender o comportamento de uma aplicação
- Inclui:
  - Logs
  - Métricas
  - Rastreamento (Tracing)

---

# Elasticsearch
- Banco de dados NoSQL orientado a documentos
- Ideal para busca e análise de grandes volumes de dados
- Armazena dados em índices e documentos
- Pode operar em cluster, garantindo alta disponibilidade

---

# Kibana
- Interface gráfica para visualização dos dados do Elasticsearch
- Permite criar dashboards e explorar logs e métricas
- Oferece visualização em tempo real e filtros avançados

---

# Elastic Stack Completo
- Elasticsearch: Armazena e analisa dados
- Kibana: Visualiza e explora dados
- Logstash: Processa e transforma dados
- Beats: Coleta dados de diversas fontes
- Elastic Agent: Coleta unificada (logs, métricas)
- X-Pack: Segurança, IA e Machine Learning

---

# Visualização do Elastic Stack
![Imagem do Elastic Stack Completo](https://tse2.mm.bing.net/th?id=OIP.7U2iS8pjA_4MzHBw4ti99gHaEU&pid=Api)
- Uma solução integrada para observabilidade, segurança e busca.

---

# Como Elasticsearch e Kibana funcionam juntos?
- Elasticsearch: Armazena e indexa os dados
- Kibana: Visualiza e analisa os dados
- Em um cluster, você pode visualizar logs de várias aplicações em um único painel

---

# Configurando Elasticsearch e Kibana
- Usaremos Docker para facilitar o setup
```bash
docker network create elastic
docker run -d --name elasticsearch --network elastic -p 9200:9200 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:8.6.2
docker run -d --name kibana --network elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.6.2
```

---

# Logback - Sistema de Logging do Spring Boot

- Logback é o sistema de logging padrão no Spring Boot.
- Oferece alta performance e flexibilidade.
- Permite configurar múltiplos destinos (arquivo, console, Elasticsearch).

---

# Por que Usar o Logback?
- É integrado nativamente ao Spring Boot.
- Suporte para diferentes formatos de logs (JSON, texto).
- Permite enviar logs diretamente para o Elasticsearch.

---

# Enviando Logs do Spring Boot para o Elasticsearch

- Usaremos o Logback, que é o sistema de logging padrão do Spring Boot.
- O Logback permite enviar logs diretamente para o Elasticsearch via TCP.

---

# Configuração Básica - Logs em Texto
- Usaremos o arquivo `logback-spring.xml` para configurar o Logback.
- O arquivo deve ser colocado em `src/main/resources`.

```xml
<configuration>
  <appender name="ELASTIC" class="ch.qos.logback.classic.net.SocketAppender">
    <remoteHost>localhost</remoteHost>
    <port>9200</port>
    <encoder>
      <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="info">
    <appender-ref ref="ELASTIC" />
  </root>
</configuration>
```

- Isso enviará os logs diretamente para o Elasticsearch na porta 9200.

---

# Configuração Avançada - Logs em JSON
- Para enviar logs em JSON, adicionaremos a dependência do JSON Encoder:

### Maven
```xml
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>7.2</version>
</dependency>
```

### Gradle
```gradle
implementation 'net.logstash.logback:logstash-logback-encoder:7.2'
```

---

# Configurando o Logback para Logs JSON
```xml
<configuration>
  <appender name="ELASTIC_JSON" class="ch.qos.logback.classic.net.SocketAppender">
    <remoteHost>localhost</remoteHost>
    <port>9200</port>
    <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
      <providers>
        <timestamp>
          <timeZone>UTC</timeZone>
        </timestamp>
        <message />
        <loggerName />
        <threadName />
        <level />
        <mdc />
        <contextName />
      </providers>
    </encoder>
  </appender>

  <root level="info">
    <appender-ref ref="ELASTIC_JSON" />
  </root>
</configuration>
```

---

# Verificando Logs no Elasticsearch
- Verificar se o Elasticsearch está rodando na porta 9200:

```bash
curl -X GET "http://localhost:9200/_cat/indices?v"
```

- Verificar se os logs estão chegando:

```bash
curl -X GET "http://localhost:9200/logs-*/_search?pretty"
```

---

# Diagnóstico de Problemas
- **Problema:** Logs não aparecem no Elasticsearch.  
  **Solução:** Verificar se o Elasticsearch está rodando e a porta está correta.

- **Problema:** Erro de conexão com o Elasticsearch.  
  **Solução:** Verificar o `remoteHost` e a porta no `logback-spring.xml`.

- **Problema:** Logs JSON aparecem como texto.  
  **Solução:** Verificar se o JSON Encoder está configurado corretamente.

---

# Boas Práticas de Configuração
- Mantenha os logs locais (arquivo) além de enviá-los para o Elasticsearch.
- Configure o nível de logs corretamente (`info`, `warn`, `error`).
- Use o JSON Encoder para enviar logs estruturados (recomendado).

---

# Como Funciona o SocketAppender?
- O `SocketAppender` envia logs diretamente para um servidor via TCP.
- No exemplo, está configurado para enviar para o Elasticsearch.

- **Remote Host:** localhost (Elasticsearch na mesma máquina).
- **Porta:** 9200 (padrão do Elasticsearch).

---

# Enviando Logs para o Elasticsearch
- A configuração acima envia logs diretamente para o Elasticsearch.
- É necessário garantir que o Elasticsearch está rodando na porta 9200.
- Verifique a configuração do Docker:

```bash
docker run -d --name elasticsearch -p 9200:9200 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:8.6.2
```

---

# Como Garantir que os Logs Também Fiquem Locais?
- Você pode configurar múltiplos appenders no `logback-spring.xml`.

```xml
<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>logs/app.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>logs/app.%d{yyyy-MM-dd}.log</fileNamePattern>
    </rollingPolicy>
    <encoder>
        <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n</pattern>
    </encoder>
</appender>

<root level="info">
    <appender-ref ref="FILE" />
    <appender-ref ref="ELASTIC" />
</root>
```

---

# Diagnóstico de Problemas com Logback
- **Problema:** Logs não aparecem no Elasticsearch.  
  **Solução:** Verificar se o Elasticsearch está rodando e a porta 9200 está aberta.

- **Problema:** Erro de conexão com o Elasticsearch.  
  **Solução:** Verificar o `remoteHost` e a porta no `logback-spring.xml`.

- **Problema:** Logs locais não estão sendo gerados.  
  **Solução:** Verificar se o appender `FILE` está configurado corretamente.

---

# Boas Práticas de Configuração
- Sempre mantenha uma cópia local dos logs (appenders múltiplos).
- Configure o nível de logs corretamente (`info`, `warn`, `error`).
- Monitore o desempenho para evitar sobrecarregar o Elasticsearch.

---


# Capturando Logs com Spring Boot
- Usaremos o Logback configurado para enviar logs ao Elasticsearch
- Configuração de exemplo:
```xml
<appender name="ELASTIC" class="ch.qos.logback.classic.net.SocketAppender">
    <remoteHost>localhost</remoteHost>
    <port>9200</port>
    <encoder>
        <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n</pattern>
    </encoder>
</appender>
```
- Os logs podem ser mantidos localmente e enviados para o Elasticsearch simultaneamente

---

# Visualizando Logs no Kibana
- Criar um índice de logs
- Usar filtros para explorar os logs
- Criar dashboards para monitoramento
- Visualizar logs em tempo real

---

# Demonstração Prática
- Configurar Elasticsearch e Kibana
- Enviar logs de uma aplicação Spring Boot
- Visualizar e analisar os logs no Kibana

---

# IA no Elastic Stack
- Elastic AI Assistant (Assistente de IA)
- Elastic Rerank e ELSER (Modelos de IA pré-treinados)
- Detecção de Anomalias e Previsão de Falhas
- AIOps e Automação de Respostas

### 🎥 Vídeo de Demonstração
- [Elastic AI Assistant para Observabilidade (YouTube)](https://www.youtube.com/watch?v=n9Gbp5NFS8s&utm_source=chatgpt.com)

---

# Segurança e Conformidade
- A Elastic protege seus modelos com licenciamento
- Verificação de licença no cluster
- Termos de uso claros para evitar reutilização

---

# Elastic Stack no Mercado
- Reconhecido como Líder no Magic Quadrant da Gartner para Observabilidade
- Amplamente adotado em setores como Finanças, Tecnologia e Saúde
- Flexível e escalável
- [Relatório Gartner - Elastic como Líder em Observabilidade](https://www.elastic.co/resources/observability/analyst-report/gartner-magic-quadrant-observability-platforms)

---

# Perguntas e Respostas Frequentes
**Q:** Os logs da aplicação ficam apenas no Elasticsearch?
**A:** Não, eles também podem ficar no arquivo padrão de logs.

**Q:** O que é um cluster?
**A:** Um conjunto de servidores que trabalham juntos para armazenar e processar dados de forma distribuída.

**Q:** Por que Elasticsearch + Kibana são tão úteis em um cluster?
**A:** Porque permitem armazenar e consultar grandes volumes de dados distribuídos, garantindo alta disponibilidade e desempenho.

**Q:** O Elasticsearch é apenas para logs?
**A:** Não, ele é uma poderosa ferramenta de busca e análise de dados.

---

# Perguntas e Respostas Frequentes
**Q:** Preciso usar Spring Boot para integrar com Elasticsearch?
**A:** Não, você pode integrar com qualquer linguagem que suporte HTTP.

**Q:** O Kibana é pago?
**A:** Existe uma versão gratuita, mas algumas funcionalidades avançadas são pagas.

**Q:** Como garantir que meus logs não ocupem muito espaço?
**A:** Configurar políticas de retenção no Elasticsearch.

**Q:** É obrigatório usar JSON nos logs enviados para o Elasticsearch?
**A:** Não, mas é o formato mais eficiente para indexação.

---

# Perguntas e Respostas Frequentes
**Q:** Como pesquisar por um log específico no Kibana?
**A:** Usando o campo de busca com filtros.

**Q:** Posso visualizar os logs em tempo real no Kibana?
**A:** Sim, usando a visualização de Logs em tempo real.

**Q:** Como garantir que apenas usuários autorizados vejam os dashboards?
**A:** Configurando controle de acesso no Kibana.

---



# Dúvidas?
- Feedback?
