// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract TaskManager {
    struct Task {
        uint id;
        string description;
        address creator;
        address assignedTo;
        bool completed;
    }

    mapping(uint => Task) public tasks;
    uint public totalTasks;

    event TaskCreated(uint indexed id, string description, address indexed creator, address indexed assignedTo);
    event TaskCompleted(uint indexed id, address indexed completedBy);

    constructor() {
        totalTasks = 0;
    }

    function createTask(string memory _description, address _assignedTo) external {
        require(_assignedTo != address(0), "Invalid assigned address");
        totalTasks++;
        tasks[totalTasks] = Task(totalTasks, _description, msg.sender, _assignedTo, false);
        emit TaskCreated(totalTasks, _description, msg.sender, _assignedTo);
    }

    function markTaskAsCompleted(uint _taskId) external {
        require(_taskId > 0 && _taskId <= totalTasks, "Invalid task ID");
        Task storage task = tasks[_taskId];
        require(msg.sender == task.assignedTo, "Only the assigned user can mark the task as completed");
        require(!task.completed, "Task is already completed");
        task.completed = true;
        emit TaskCompleted(_taskId, msg.sender);
    }
}
