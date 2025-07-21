#include <iostream>
#include <vector>
#include <algorithm>
#include <iomanip>
#include <random>

using namespace std;

struct Process {
    string name;
    int arrival;
    int burst;
    int remaining;
    int completion;
    int turnaround;
    int start;
    int active;

    Process(string n, int a, int b)
        : name(n), arrival(a), burst(b), remaining(b), completion(0),
        turnaround(0), start(-1), active(1) {}
};

// Generate random processes
vector<Process> generateProcesses(int n, int k, double d, double v) {
    random_device rd;
    mt19937 gen(rd());
    uniform_int_distribution<> arrivalDist(0, k);
    normal_distribution<> burstDist(d, v);

    vector<Process> processes;
    for (int i = 0; i < n; ++i) {
        int arrival = arrivalDist(gen);
        int burst;
        do { burst = static_cast<int>(burstDist(gen)); } while (burst <= 0);
        processes.emplace_back("P" + to_string(i + 1), arrival, burst);
    }
    return processes;
}

// Detailed process table
void printDetailedTable(const vector<Process>& processes) {
    cout << "\nFinal Process Table:\n";
    cout << left << setw(10) << "Process"
         << setw(10) << "Active"
         << setw(15) << "Arrival Time"
         << setw(18) << "Total CPU Time"
         << setw(22) << "Remaining CPU Time"
         << setw(18) << "Turnaround Time" << "\n";

    for (const auto& p : processes) {
        cout << left << setw(10) << p.name
             << setw(10) << p.active
             << setw(15) << p.arrival
             << setw(18) << p.burst
             << setw(22) << p.remaining
             << setw(18) << p.turnaround << "\n";
    }
}

// FIFO Scheduling
double fifoScheduling(vector<Process> processes) {
    sort(processes.begin(), processes.end(), [](Process a, Process b) { return a.arrival < b.arrival; });
    int time = 0, totalTAT = 0;
    cout << "\nFIFO Gantt Chart:\n0 ";
    for (auto& p : processes) {
        if (time < p.arrival) time = p.arrival;
        cout << "| " << p.name << " | " << time + p.burst << " ";
        time += p.burst;
        p.completion = time;
        p.remaining = 0;
        p.turnaround = p.completion - p.arrival;
        totalTAT += p.turnaround;
        p.active = 0;
    }
    cout << "\n";
    printDetailedTable(processes);
    return (double)totalTAT / processes.size();
}

// SJF Scheduling
double sjfScheduling(vector<Process> processes) {
    int time = 0, totalTAT = 0;
    vector<Process> completed;
    cout << "\nSJF Gantt Chart:\n0 ";
    while (completed.size() < processes.size()) {
        vector<Process*> ready;
        for (auto& p : processes)
            if (p.arrival <= time && p.remaining > 0)
                ready.push_back(&p);

        if (ready.empty()) { time++; continue; }

        sort(ready.begin(), ready.end(), [](Process* a, Process* b) { return a->burst < b->burst; });

        Process* p = ready.front();
        if (time < p->arrival) time = p->arrival;
        cout << "| " << p->name << " | " << time + p->burst << " ";
        time += p->burst;
        p->completion = time;
        p->turnaround = p->completion - p->arrival;
        p->remaining = 0;
        p->active = 0;
        completed.push_back(*p);
    }
    cout << "\n";
    printDetailedTable(processes);
    for (const auto& p : processes) totalTAT += p.turnaround;
    return (double)totalTAT / processes.size();
}

// SRT Scheduling
double srtScheduling(vector<Process> processes) {
    int time = 0, totalTAT = 0, complete = 0, n = processes.size();
    cout << "\nSRT Gantt Chart:\n0 ";
    while (complete != n) {
        int shortest = -1;
        for (int j = 0; j < n; j++)
            if (processes[j].arrival <= time && processes[j].remaining > 0)
                if (shortest == -1 || processes[j].remaining < processes[shortest].remaining)
                    shortest = j;

        if (shortest == -1) { time++; continue; }

        cout << "| " << processes[shortest].name << " ";
        int start_time = time;
        while (processes[shortest].remaining > 0) {
            time++;
            processes[shortest].remaining--;
            bool preempt = false;
            for (int j = 0; j < n; j++)
                if (processes[j].arrival == time && processes[j].remaining < processes[shortest].remaining)
                    preempt = true;
            if (preempt) break;
        }
        cout << "| " << time << " ";
        if (processes[shortest].remaining == 0) {
            processes[shortest].completion = time;
            processes[shortest].turnaround = time - processes[shortest].arrival;
            processes[shortest].active = 0;
            totalTAT += processes[shortest].turnaround;
            complete++;
        }
    }
    cout << "\n";
    printDetailedTable(processes);
    return (double)totalTAT / n;
}

int main() {
    int n = 10;    // Number of processes
    int k = 20;    // Arrival time window [0, 20]
    double d = 10; // Mean burst time
    double v = 5;  // Std dev for burst time

    vector<Process> baseProcesses = generateProcesses(n, k, d, v);

    cout << "\nGenerated Process Table:\n";
    cout << left << setw(10) << "Process" << setw(15) << "Arrival Time" << setw(15) << "CPU Time" << "\n";
    for (const auto& p : baseProcesses) {
        cout << left << setw(10) << p.name << setw(15) << p.arrival << setw(15) << p.burst << "\n";
    }

    double fifoAvg = fifoScheduling(baseProcesses);
    double sjfAvg  = sjfScheduling(baseProcesses);
    double srtAvg  = srtScheduling(baseProcesses);

    cout << "\n--- Comparative Analysis ---\n";
    cout << left << setw(10) << "Algorithm" << setw(25) << "Avg Turnaround Time" << "\n";
    cout << setw(10) << "FIFO" << setw(25) << fifoAvg << "\n";
    cout << setw(10) << "SJF"  << setw(25) << sjfAvg  << "\n";
    cout << setw(10) << "SRT"  << setw(25) << srtAvg  << "\n";

    return 0;
}
