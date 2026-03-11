# Testes e Assertividade

## Tabela de Conteúdos <!-- omit in toc -->

- [Testes Unitários](#testes-unitários)
- [Testes End-to-End (E2E)](#testes-e2e)
- [Rodando Rotinas Independentes c/ Docker](#testes-em-conteineres-dockere2e)

---

A filosofia do Boilerplate foca em permitir a checagem rápida em ambientes que mimetizam rigorosamente o Comportamento Esperado usando os Contêineres. 

## Testes Unitários

Verificará rapidamente via JEST se os Services internos/Mock Providers (e seu código isolado) estão funcionando.
```bash
npm run test
```

## Testes E2E

Verificará todo o roteamento do servidor da App, através do `Supertest`, conectando no banco. A diferença é que a cada suite E2E a aplicação derruba a si própria e reconstroi para garantir "Cenários Zerados e Limpos".

```bash
npm run test:e2e
```

## Testes em Conteineres (Docker/E2E)

### Para o ecossistema atual configurado com PostgreSQL (Relacional)

O comando final abaixo irá submeter e empacotar uma build local novinha focada apenas para o E2E testar na íntegra a arquitetura Docker de forma 100% igual ao pipeline de CI no GitHub! Esse roteiro de Script (`test:e2e:relational:docker`) inclusive inicia seu container, prepara o BD paralelo, aguarda o healthcheck através da biblioteca de espera unificada `wait-for-it.sh`, e roda a bateria! Tendo concluído a bateria, o conteiner de "Test-E2E" é derrubado sem impactar suas tabelas da máquina real!

```bash
npm run test:e2e:relational:docker
```
