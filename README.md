# Jornada-do-Heroi
# Questões 1–3: Heróis básicos
class Guerreiro:
    def __init__(self, nome, vida, ataque):
        self.nome = nome
        self.vida = vida
        self.ataque_base = ataque
        self.arma = None

    def __str__(self):
        return f"Guerreiro: {self.nome}, Vida: {self.vida}, Ataque: {self.ataque_base}"

    def atacar(self, alvo):
        dano = self.ataque_base + (self.arma.dano if self.arma else 0)
        alvo.receber_dano(dano)

class Mago:
    def __init__(self, nome, vida, ataque, poder_magico):
        self.nome = nome
        self.vida = vida
        self.ataque_base = ataque
        self.poder_magico = poder_magico
        self.arma = None

    def __str__(self):
        return f"Mago: {self.nome}, Vida: {self.vida}, Magia: {self.poder_magico}"

    def atacar(self, alvo):
        dano = self.ataque_base + self.poder_magico + (self.arma.dano if self.arma else 0)
        alvo.receber_dano(dano)

class Arqueiro:
    def __init__(self, nome, vida, ataque, precisao):
        self.nome = nome
        self.vida = vida
        self.ataque_base = ataque
        self.precisao = precisao
        self.arma = None

    def __str__(self):
        return f"Arqueiro: {self.nome}, Vida: {self.vida}, Precisão: {self.precisao}"

    def atacar(self, alvo):
        dano = self.ataque_base + self.precisao + (self.arma.dano if self.arma else 0)
        alvo.receber_dano(dano)

# Questão 4: Monstro e Goblin
class Monstro:
    def __init__(self, nome, vida, ataque):
        self.nome = nome
        self.vida = vida
        self.ataque = ataque

    def __str__(self):
        return f"{self.nome} (Vida: {self.vida})"

    def atacar(self, alvo):
        alvo.receber_dano(self.ataque)

    def receber_dano(self, dano):
        self.vida = max(0, self.vida - dano)

Goblin = Monstro("Goblin", 30, 5)

# Questões 7–8: Armas e Poções
class Arma:
    def __init__(self, nome, dano):
        self.nome = nome
        self.dano = dano

espada_longa = Arma("Espada Longa", 10)
cajado_magico = Arma("Cajado Mágico", 8)

class Pocao:
    def __init__(self, nome, cura):
        self.nome = nome
        self.cura = cura

pocao_vida = Pocao("Poção de Vida", 20)

# Questões 9–20: Estrutura Personagem + Inventário
class Personagem:
    def __init__(self, nome, vida, ataque):
        self._vida = vida
        self.vida_max = vida
        self.nome = nome
        self.ataque_base = ataque
        self.arma = None
        self.inventario = Inventario()
        self.habilidades = []

    @property
    def vida(self):
        return self._vida

    @vida.setter
    def vida(self, valor):
        self._vida = max(0, min(valor, self.vida_max))

    def receber_dano(self, dano):
        self.vida -= dano

    def esta_vivo(self):
        return self.vida > 0

    def atacar(self, alvo):
        dano = self.ataque_base + (self.arma.dano if self.arma else 0)
        alvo.receber_dano(dano)

class Inventario:
    def __init__(self):
        self.itens = []

    def adicionar(self, item):
        self.itens.append(item)

    def usar_pocao(self, personagem):
        for item in self.itens:
            if isinstance(item, Pocao):
                personagem.vida += item.cura
                self.itens.remove(item)
                break

def criar_goblin_padrao():
    return Monstro("Goblin", 30, 5)

# Questões 21–23: Habilidades
from abc import ABC, abstractmethod

class Habilidade(ABC):
    @abstractmethod
    def usar(self, usuario, alvo):
        pass

class AtaqueForte(Habilidade):
    def usar(self, usuario, alvo):
        dano = (usuario.ataque_base * 2)
        alvo.receber_dano(dano)

class BolaDeFogo(Habilidade):
    def usar(self, usuario, alvo):
        dano = 25
        alvo.receber_dano(dano)

# Questões 24–25: Dado e dano aleatório
import random

class Dado:
    @staticmethod
    def rolar(lados=6):
        return random.randint(1, lados)

# Questão 26: Orc com golpe crítico
class Orc(Monstro):
    def atacar(self, alvo):
        dano = self.ataque
        if random.random() < 0.3:
            dano *= 2
        alvo.receber_dano(dano)

# Questões 27–30: Sistema de batalha
class Batalha:
    def __init__(self, combatente1, combatente2):
        self.c1 = combatente1
        self.c2 = combatente2

    def turno(self):
        self.c1.atacar(self.c2)
        if not self.c2.esta_vivo():
            return
        self.c2.atacar(self.c1)

    def lutar(self):
        while self.c1.esta_vivo() and self.c2.esta_vivo():
            self.turno()
        return self.c1 if self.c1.esta_vivo() else self.c2

class BatalhaEquipe:
    def __init__(self, equipe1, equipe2):
        self.e1 = equipe1
        self.e2 = equipe2

    def lutar(self):
        while any(p.esta_vivo() for p in self.e1) and any(p.esta_vivo() for p in self.e2):
            a = next(p for p in self.e1 if p.esta_vivo())
            b = next(p for p in self.e2 if p.esta_vivo())
            a.atacar(b)
            if b.esta_vivo():
                b.atacar(a)
        return "Equipe 1" if any(p.esta_vivo() for p in self.e1) else "Equipe 2"
