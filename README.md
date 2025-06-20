# Genetic_Algorithm_RCPSP
# Genetic Algorithm for Resource-Constrained Project Scheduling Problem (SRCPSP)

This project implements a genetic algorithm approach to solve the Resource-Constrained Project Scheduling Problem (SRCPSP), which is a well-known NP-hard optimization problem in operations research and project management.

*Note: The optimized schedule can be viewed in the generated gantt_optimized.pdf file after running the visualization script.*

## Project Overview

The Resource-Constrained Project Scheduling Problem involves scheduling a set of tasks/activities with specified durations and resource requirements, while respecting precedence constraints (task dependencies) and resource limitations. The objective is typically to minimize the overall project makespan (completion time).

### Key Features

- **Genetic Algorithm Optimization**: Employs evolutionary computing to find near-optimal schedules
- **Resource Constraint Handling**: Manages limited resource availability across project tasks
- **Schedule Visualization**: Generates detailed Gantt charts to visualize task schedules
- **Performance Metrics**: Calculates and reports key schedule performance indicators
- **Comparison Analysis**: Demonstrates optimization benefits against non-optimized schedules

## Detailed Background and Algorithm Logic

### The RCPSP Problem in Depth

The Resource-Constrained Project Scheduling Problem is formally defined as:
- A set of tasks T = {1, 2, ..., n} with durations d = {d₁, d₂, ..., dₙ}
- A set of resources R = {1, 2, ..., m} with capacities c = {c₁, c₂, ..., cₘ}
- Precedence constraints represented as a directed acyclic graph
- Resource requirements for each task-resource pair

The goal is to find a schedule (start times for all tasks) that:
1. Respects all precedence constraints (a task starts only after all its predecessors finish)
2. Ensures resource usage at any time point doesn't exceed capacity
3. Minimizes the project makespan (completion time of the last task)

What makes this problem challenging is that it's NP-hard, meaning there's no known polynomial-time algorithm to find the optimal solution for all instances. For real-world projects with dozens or hundreds of tasks, exhaustive search becomes computationally infeasible.

### Genetic Algorithm Approach: Detailed Logic

Our implementation uses a genetic algorithm with the following specific components:

#### 1. Chromosome Representation
Each potential solution (chromosome) represents a schedule as a priority list of tasks. This is a permutation of tasks that defines the order in which they will be scheduled by the scheduler.

#### 2. Population Initialization
- Population size: Configurable, default is 20 individuals
- Initial population: Randomly generated permutations of tasks
- Each permutation respects the precedence constraints (topological ordering)

#### 3. Fitness Function
The fitness of a schedule is determined by:
- Primary objective: Total project duration (lower is better)
- Secondary considerations: Resource utilization and task parallelization

#### 4. Selection Mechanism
- Tournament selection: Randomly select k individuals and choose the best one
- Selection pressure: Configurable, balances exploration vs. exploitation
- Elitism: The best solutions from each generation are preserved

#### 5. Genetic Operators
- **Crossover**: Precedence Preserving Order-Based Crossover (POX)
  - Selects tasks from one parent and preserves their order
  - Fills remaining positions with tasks from the second parent
  - Maintains precedence feasibility
  
- **Mutation**: Swap Mutation with Precedence Check
  - Randomly selects two positions in the chromosome
  - Swaps their values if the swap doesn't violate precedence constraints
  - Mutation rate: Configurable, typically set to 0.05-0.1

#### 6. Schedule Construction
For each chromosome (priority list):
1. Sort tasks according to the priority list
2. Schedule each task as early as possible while:
   - Respecting precedence constraints (all predecessors completed)
   - Ensuring resource availability (no overallocation)
3. Calculate the resulting schedule's makespan and resource metrics

#### 7. Termination Criteria
The algorithm terminates when one of these conditions is met:
- Maximum number of generations reached (configurable)
- No improvement in the best solution for a specified number of generations
- Optimal solution found (if known)

#### 8. Parallelization
The algorithm can utilize multiple CPU cores to evaluate different chromosomes in parallel, significantly speeding up the search process for large problem instances.

### Evolution Process Walkthrough

1. **Initialization**: Create random task priority lists (respecting precedence)
2. **Evaluation**: Calculate schedule quality (makespan) for each list
3. **Selection**: Choose better-performing schedules for reproduction
4. **Crossover**: Combine good solutions to create new ones
5. **Mutation**: Add small random changes to maintain diversity
6. **Replacement**: Replace old population with new offspring
7. **Repeat**: Continue the process for multiple generations
8. **Output**: Return the best schedule found

This approach effectively balances exploration (finding new promising areas) and exploitation (improving known good solutions), allowing it to escape local optima and find near-optimal solutions for complex scheduling problems.

## Project Structure

```
Genetic-SRCPSP/
├── deepThought/              # Core implementation modules
│   ├── optimizer/            # Genetic algorithm implementation
│   ├── scheduler/            # Task scheduling algorithms
│   ├── simulator/            # Simulation engine for schedule execution
│   ├── visualizer/           # Visualization and Gantt chart generation
│   └── ORM/                  # Object-relational mapping for data models
├── sampleData/               # Example project datasets
├── run_visualizer.py         # Script for generating visualizations
├── output.pickle             # Simulation results from the most recent run
├── gantt_optimized.pdf       # Visualization of the optimized schedule
├── gantt_non_optimized.pdf   # Visualization of a non-optimized schedule
└── comparison_chart.pdf      # Performance comparison between schedules
```

## Data Model

The project works with the following data entities:

- **Tasks**: Individual activities with durations, dependencies, and resource requirements
- **Resources**: Entities with limited availability that tasks require to execute
- **Dependencies**: Predecessor-successor relationships between tasks
- **Schedule**: A solution representing start/end times for all tasks

## Quick Start Guide

```bash
# Clone the repository
[git clone https://github.com/AKHIL-514/Genetic_Algorithm_RCPSP.git]
cd Genetic_Algorithm_RCPSP

# Create and activate virtual environment
python -m venv venv
.\venv\Scripts\Activate

# Install dependencies
pip install -r requirements.txt

# Run the genetic algorithm scheduler
python -m deepThought.simulator.simulator --scheduler PPPolicies --output output.pickle sampleData/test_data.pickle

# Generate visualizations
python run_visualizer.py

# View the results
start gantt_optimized.pdf
start gantt_non_optimized.pdf
start comparison_chart.pdf
```

## Understanding the Output

### Visualization Files

The visualization process generates several files:

1. **gantt_optimized.pdf**: Gantt chart showing the optimized schedule produced by the genetic algorithm
   - Color-coded by resource usage
   - Includes task durations and relationships
   - Contains summary metrics on project performance

2. **gantt_non_optimized.pdf**: Gantt chart showing a non-optimized schedule for comparison
   - Uses the same task set but with suboptimal scheduling
   - Demonstrates increased project duration and resource conflicts

3. **comparison_chart.pdf**: Bar chart comparing key performance metrics
   - Project duration reduction
   - Resource utilization improvement
   - Task parallelization comparison

### Example Output Interpretation

When you run the visualizer, you'll see output like this:

```
Loaded simulation result with 13 tasks
Total duration: 279.41

TOP RESOURCES USED IN SCHEDULE:
  1. Resource 3: Used 4 times
  2. Resource 1: Used 4 times
  3. Resource 6: Used 4 times
  
COMPARISON RESULTS:
Duration: 279.4 vs 470.5 time units (40.6% improvement)
Resource Utilization: 45.7% vs 38.0% (20.3% improvement)
Avg Concurrent Tasks: 2.44 vs 2.08 (17.5% improvement)
```

This shows that the genetic algorithm achieved a 40.6% reduction in project duration compared to a non-optimized approach.

### Performance Metrics

The visualizations and console output include several key performance indicators:

- **Total Duration**: Overall project completion time (lower is better)
- **Resource Utilization**: Percentage of available resource time effectively used
- **Average Concurrent Tasks**: Measure of task parallelization
- **Critical Path Tasks**: Tasks that directly impact the project completion time

## Sample Results

In a typical run with the provided test data, the genetic algorithm achieves:

- **Duration Reduction**: 30-40% shorter project completion time
- **Resource Utilization**: 15-25% improvement in resource usage efficiency
- **Task Parallelization**: Enhanced concurrent task execution

## Visualization Examples

After running the visualizer, the generated PDFs will look similar to these:

### Optimized Schedule
The optimized schedule shows efficient resource usage and task parallelization with a shorter overall duration.

*View `gantt_optimized.pdf` for a color-coded Gantt chart of the optimized schedule.*

![Optimized Schedule Gantt Chart](docs/images/gantt_optimized.jpg)
*Sample optimized schedule Gantt chart showing efficient resource allocation and task parallelization*

### Non-Optimized Schedule
The non-optimized schedule shows less efficient resource allocation and longer project duration.

*View `gantt_non_optimized.pdf` for comparison.*

![Non-Optimized Schedule Gantt Chart](docs/images/gantt_non_optimized.jpg)
*Sample non-optimized schedule showing longer project duration and less efficient resource usage*

### Performance Comparison
The comparison chart provides a clear visualization of the improvements achieved by the genetic algorithm.

*View `comparison_chart.pdf` for a bar chart comparing key metrics.*

![Performance Comparison Chart](docs/images/comparison_chart.jpg)
*Bar chart comparing key performance metrics between optimized and non-optimized schedules*

## Real-World Applications

This type of optimization is valuable in:

- **Construction Projects**: Optimizing use of equipment and labor
- **Software Development**: Managing developer assignments across sprints
- **Manufacturing**: Production planning with machine constraints
- **Event Planning**: Coordinating vendors and setup crews
- **Research Projects**: Allocating lab equipment and researcher time

## Limitations

- Does not account for partial resource allocation
- Assumes deterministic task durations (no uncertainty)
- May not find the global optimum due to the NP-hard nature of the problem

## References

- Hartmann, S. (1998). "A competitive genetic algorithm for resource-constrained project scheduling"
- Kolisch, R., & Hartmann, S. (2006). "Experimental investigation of heuristics for resource-constrained project scheduling"

## License

This project is licensed under the MIT License - see the LICENSE file for details.


