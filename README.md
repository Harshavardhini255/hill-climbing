# hill-climbing
This program implements the Hill Climbing algorithm to solve the Travelling Salesman Problem (TSP) using Python and Streamlit. It starts with a random tour and iteratively improves it by swapping cities to find a shorter route. The best neighboring solution is chosen at each step until no further improvement is possible. 
import random
import streamlit as st
import numpy as np
import ast

def random_solution(w):
    cities = list(range(len(w)))
    solution = random.sample(cities, len(cities))
    return solution

def route_length(w, solution):
    route_length = 0
    n = len(solution)
    for i in range(n):
        route_length += w[solution[i - 1]][solution[i]]
    return route_length

def get_neighbours(solution):
    neighbours = []
    n = len(solution)
    for i in range(n):
        for j in range(i + 1, n):
            neighbour = solution.copy()
            neighbour[i], neighbour[j] = neighbour[j], neighbour[i]
            neighbours.append(neighbour)
    return neighbours

def get_best_neighbour(w, neighbours):
    best_route_length = route_length(w, neighbours[0])
    best_neighbour = neighbours[0]
    for neighbour in neighbours:
        current_route_length = route_length(w, neighbour)
        if current_route_length < best_route_length:  # Fixed condition
            best_route_length = current_route_length
            best_neighbour = neighbour
    return best_neighbour, best_route_length

def hill_climbing(w, initial_solution, max_iterations=1000):
    current_solution = initial_solution
    current_length = route_length(w, current_solution)
    for i in range(max_iterations):
        neighbours = get_neighbours(current_solution)
        best_neighbour, best_length = get_best_neighbour(w, neighbours)
        if best_length < current_length:
            current_solution = best_neighbour
            current_length = best_length
        else:
            break
    return current_solution, current_length

def main():
    st.title("Hill Climbing - Travelling Salesman Problem")
    
    w = st.text_input("Enter distance matrix (e.g., [[0,10,15],[10,0,35],[15,35,0]])")
    
    if st.button("Generate Random Solution"):
        try:
            w = ast.literal_eval(w)
            solution = random_solution(w)
            length = route_length(w, solution)
            st.write(f"**Random Solution:** {solution}")
            st.write(f"**Route Length:** {length}")
        except Exception as e:
            st.write("Invalid input. Please enter a valid distance matrix.")
    
    if st.button("Run Hill Climbing"):
        try:
            w = ast.literal_eval(w)
            initial_solution = random_solution(w)
            st.write(f"**Initial Random Solution:** {initial_solution}")
            st.write(f"**Initial Route Length:** {route_length(w, initial_solution)}")
            solution, length = hill_climbing(w, initial_solution)
            st.write(f"**Best Solution After Hill Climbing:** {solution}")
            st.write(f"**Best Route Length:** {length}")
        except Exception as e:
            st.write("Invalid input. Please enter a valid distance matrix.")

if __name__ == "__main__":
    main()
