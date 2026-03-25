# Tech-pizza-app
primeira parte do app de pizzaria em python


from http.client import CREATED
import flet as ft
import sqlite3
import datetime

conn = sqlite3.connect("pizzas.db", check_same_thread=False)
cursor = conn.cursor()

cursor.execute("""
    CREATE TABLE IF NOT EXISTS pizzas (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT,
    preco REAL
    )
""")
conn.commit()

# Inserir pizzas (se estiver vazio)
cursor.execute("SELECT COUNT(*) FROM pizzas")
if cursor.fetchone()[0] == 0:
    pizzas_iniciais = [
        ("Calabresa", 35),
        ("Mussarela", 30),
        ("Frango com Catupiry", 40),
    ]
    cursor.executemany("INSERT INTO pizzas (nome, preco) VALUES (?, ?)", pizzas_iniciais)
    conn.commit()

# LÓGICA IA (COMBO DO DIA)
def combo_do_dia():
    dia = datetime.datetime.today().weekday()  # 0 = segunda

    combos = {
        0: "Calabresa",
        1: "Mussarela",
        2: "Frango com Catupiry"
    }

    return combos[dia]


def main(page: ft.Page):
    page.title = "Tech-Pizza "

    cursor.execute("SELECT nome, preco FROM pizzas")
    pizza = cursor.fetchall()

    lista = []
    for nome, preco in pizza:
        lista.append(ft.Text(f"{nome} - R$ {preco}"))

    combo = combo_do_dia()

    def pedir(e):
        page.snack_bar = ft.SnackBar(
            content=ft.Text("Pedido realizado!")
        )
        page.snack_bar.open = True
        page.update()

    page.add(
        ft.Text(" Tech-Pizza", size=30, weight="bold"),
        ft.Text(f" Combo do Dia: {combo}", size=20),
        ft.Text("Cardápio:", size=20),
        *lista,
        ft.ElevatedButton("Fazer Pedido", on_click=pedir)
    )

ft.app(target=main)
